# Vue中的表单校验


<!--more-->

**1、直接在组件中写rules  < :rules=' rules'>**

**2、在data中写入方法**

```js
rules {	
      name: [ { 
          type: 'string',
          required: true,
          message: "名称必填", 
          trigger: 'blur'
      }, {
          max: 30,
          message: "名称长度不能超过30位" 
      }]
}
```

其中name为prop名

type：类型

required：是否必选项（此栏是否为空）

message：报错信息

trigger：触发方式

​		blur ：失去焦点时进行验证	常用：对 input 输入框的验证

​		change ：当值发生变化时进行验证		常用：下拉框select，日期选择框date-picker，复选框checkbox，单选框radio

3、**也可以直接用pattern进行匹配验证**:

```js
name: [ { pattern: 验证条件, required: true, message: "提示信息", trigger: "blur" }]
```

```js
rules: {
    //验证非空和长度
    name: [
            {
                required: true,
                message: "站点名称不能为空",
                trigger: "blur"
            },
            {
                min: 3, 
                max: 5, 
                message: '长度在 3 到 5 个字符', 
                trigger: 'blur' 
            }
        ],
        []
    }
```



**vue常用检验**

前端Vue中常用rules校验规则:

1、是否合法IP地址:

pattern:/^(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])\.(\d{1,2}|1\d\d|2[0-4]\d|25[0-5])$/,

2.是否手机号码或者固话

pattern:/^((0\d{2,3}-\d{7,8})|(1[34578]\d{9}))$/,

3.是否身份证号码

pattern:/(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/,

4.是否邮箱

pattern:/^([a-zA-Z0-9]+[-_\.]?)+@[a-zA-Z0-9]+\.[a-z]+$/,

5.整数填写

pattern:/^-?[1-9]\d*$/,

6.正整数填写

pattern:/^[1-9]\d*$/,

7.小写字母

pattern:/^[a-z]+$/,

8.大写字母

pattern:/^[A-Z]+$/,

9.大小写混合

pattern:/^[A-Za-z]+$/,

10.多个8位数字格式(yyyyMMdd)并以逗号隔开

pattern:/^\d{8}(\,\d{8})*$/,

11.数字加英文，不包含特殊字符

pattern:/^[a-zA-Z0-9]+$/,

12.前两位是数字后一位是英文

pattern:/^\d{2}[a-zA-Z]+$/,

13.密码校验（6-20位英文字母、数字或者符号（除空格），且字母、数字和标点符号至少包含两种）

pattern:/^(?![\d]+$)(?![a-zA-Z]+$)(?![^\da-zA-Z]+$)([^\u4e00-\u9fa5\s]){6,20}$/,

14.中文校验

pattern:/^[\u0391-\uFFE5A-Za-z]+$/,
