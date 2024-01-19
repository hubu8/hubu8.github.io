# 分布式锁颗粒度小记--加锁时间过长


<!--more-->

### 业务背景

​	在某业务单据中，需要对单据进行审核操作，发现分录有1000条数据，审核时间30s,需要进行优化

​	审核插件主要进行了两个操作：

​		1、数据校验（组map,对数据进行校验）

​		2、更新中间表（同步资源余量表）

### 耗时查询

```
代码调用性能消耗报告：
kd.bos.service.ServiceFactory.FormService.batchInvokeAction(), cost 30217 ms.
|----FormService.batchInvokeAction('983ee13baa4049329a0b1f2a71ec3732'), cost 30216 ms.
    |----FormService.ecma_totalrequireplan.itemClick('ecma_totalrequireplan','ecma_totalrequireplan','tbmain'), cost 30211 ms.
        |----operate.BillView.invokeOperation('audit','ecma_totalrequireplan'), cost 30184 ms.
            |----operate.FormView.invokeOperation('audit'), cost 30183 ms.
                |----operate.FormOperate.execute('ecma_totalrequireplan','audit','Audit','kd.bos.entity.operate.Audit'), cost 30182 ms.
                    |----operate.Audit.invokeOperation(), cost 30171 ms.
                        |----ModelCache.getAll(), cost 55 ms.
                            |----DataEntitySerializer.readCollection(), cost 23 ms.
                            |----datareader.loadRefence(), cost 13 ms.
                        |----opservice.OperationService.invokeOperation('ecma_totalrequireplan','audit'), cost 30092 ms.
                            |----opservice.initialize(), cost 222 ms.
                                |----msopfacade.kd.bos.service.botp.facade.OperateBfFacade.afterInitialize(), cost 208 ms.
                                    |----FunctionManage.get(), cost 206 ms.
                            |----opservice.excute(dataEntities)(), cost 29867 ms.
                                |----opservice.doExcete(), cost 27015 ms.
                                    |----opservice.validate(), cost 61 ms.
                                        |----opvalidate.ValidationService.validate(), cost 61 ms.
                                            |----opvalidate.kd.bos.service.operation.validate.MutexValidatorvalidate(), cost 36 ms.
                                                |----MutexValidator.batchRequireMutex(), cost 36 ms.
                                    |----opplugin.cbtgc.ec.ecma.op.ZJJTTotalRequirePlanInitResourceOp.beforeExecuteOperationTransaction(), cost 26599 ms.
                                    |----opservice.beforeCallOperationTransaction(), cost 59 ms.
                                        |----msopfacade.kd.bos.service.botp.facade.OperateBfFacade.afterUpdateBillStatus(), cost 59 ms.
                                    |----opservice.callOperationTransaction(), cost 294 ms.
                                        |----opservice.TX.required(), cost 292 ms.
                                            |----opplugin.kd.ec.material.opplugin.TotalRequirePlanOp.beginOperationTransaction(), cost 82 ms.
                                                |----BusinessDataServiceHelper.loadSingle(), cost 77 ms.
                                                    |----datareader.loadSingle(), cost 50 ms.
                                            |----opplugin.cbtgc.ec.ecma.op.ZJJTTotalRequirePlanInitResourceOp.beginOperationTransaction(), cost 197 ms.
                                                |----BusinessDataServiceHelper.load(), cost 43 ms.
                                                    |----datareader.load(), cost 23 ms.
                                                        |----datareader.loadRefence(), cost 11 ms.
                                |----opplugin.cbtgc.ec.ecma.op.ZJJTTotalRequirePlanInitResourceOp.onReturnOperation(), cost 2843 ms.
        |----datamodel.updateCache(), cost 25 ms.
            |----ModelCache.storeAll(), cost 25 ms.
```

opvalidate.ValidationService.validate()：校验数据，61ms

opplugin.cbtgc.ec.ecma.op.ZJJTTotalRequirePlanInitResourceOp.beforeExecuteOperationTransaction():  更新中间表之前加分布式事务锁， 26599 ms.

opplugin.kd.ec.material.opplugin.TotalRequirePlanOp.beginOperationTransaction()：更新中间表 ，82 ms

opplugin.cbtgc.ec.ecma.op.ZJJTTotalRequirePlanInitResourceOp.onReturnOperation()：最后执行，解锁  2843ms

从以上日志分析发现，代码耗时主要发生在加分布式事务锁；分布式事务锁的颗粒度为：项目、单位工程、资源；锁的颗粒度为最细。

那么单据一共有1000个分录，就会加1000个分布式事务锁。

### 解决方案

原因：

​	分布式锁的粒度，到了boq级别，粒度很小，导致锁的数量多，获取锁和释放锁耗费大量时间； 

优化： 

1、总体需用计划生成中间表的时候，不需要加锁，因为还没有下游业务； 

2、锁的粒度加大，增加到单位工程+项目的级别，这样锁的次数只要获取一次，（合同、合同变更、招标立项、差旅报销、对公报销、费用报销的更新中间表的地方都要改，继承了AbstractResourceDetailLockOp的类）
