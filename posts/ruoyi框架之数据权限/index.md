# Ruoyi框架之数据权限


<!--more-->

在实际开发中，需要设置用户只能查看哪些部门的数据，这种情况一般称为数据权限。
例如对于销售，财务的数据，它们是非常敏感的，因此要求对数据权限进行控制， 对于基于集团性的应用系统而言，就更多需要控制好各自公司的数据了。如设置只能看本公司、或者本部门的数据，对于特殊的领导，可能需要跨部门的数据， 因此程序不能硬编码那个领导该访问哪些数据，需要进行后台的权限和数据权限的控制。

提示

默认系统管理员`admin`拥有所有数据权限`（userId=1）`，默认角色拥有所有数据权限（如不需要数据权限不用设置数据权限操作）

### 注解参数说明

| 参数      | 类型   | 默认值 | 描述         |
| --------- | ------ | ------ | ------------ |
| deptAlias | String | 空     | 部门表的别名 |
| userAlias | String | 空     | 用户表的别名 |

### 数据权限使用

1、在（系统管理-角色管理）设置需要数据权限的角色 目前支持以下几种权限

- 全部数据权限

- 自定数据权限

- 部门数据权限

- 部门及以下数据权限

- 仅本人数据权限

  ![image-20221003162100592.png](/posts/java/ruoyi/ruoyi框架之数据权限/image-20221003162100592.png)

2、在需要数据权限控制方法上添加`@DataScope`注解，其中`d`和`u`用来表示表的别名

**部门数据权限注解**

```java
@DataScope(deptAlias = "d")
public List<...> select(...)
{
    return mapper.select(...);
}
```

**部门及用户权限注解**

```java
@DataScope(deptAlias = "d", userAlias = "u")
public List<...> select(...)
{
    return mapper.select(...);
}
```

3、在`mybatis`查询底部标签添加数据范围过滤

```xml
<select id="select" parameterType="..." resultMap="...Result">
    <include refid="select...Vo"/>
    <!-- 数据范围过滤 -->
    ${params.dataScope}
</select>
```

例如：用户管理（未过滤数据权限的情况）：

```sql
--left join 只是把部门名称带过来，没有数据权限
select u.user_id, u.dept_id, u.login_name, u.user_name, u.email
	, u.phonenumber, u.password, u.sex, u.avatar, u.salt
	, u.status, u.del_flag, u.login_ip, u.login_date, u.create_by
	, u.create_time, u.remark, d.dept_name
from sys_user u
	left join sys_dept d on u.dept_id = d.dept_id
where u.del_flag = '0'
```

例如：用户管理（已过滤数据权限的情况）：

```sql
select u.user_id, u.dept_id, u.login_name, u.user_name, u.email
	, u.phonenumber, u.password, u.sex, u.avatar, u.salt
	, u.status, u.del_flag, u.login_ip, u.login_date, u.create_by
	, u.create_time, u.remark, d.dept_name
from sys_user u
	left join sys_dept d on u.dept_id = d.dept_id
where u.del_flag = '0'
	and u.dept_id in (
		select dept_id
		from sys_role_dept
		where role_id = 2
	)
```

结果很明显，我们多了如下语句。通过角色部门表`（sys_role_dept）`完成了数据权限过滤

```sql
and u.dept_id in (
	select dept_id
	from sys_role_dept
	where role_id = 2
)
```

逻辑实现代码 `com.ruoyi.framework.aspectj.DataScopeAspect`

```java
package com.ruoyi.common.datascope.aspect;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;
import com.ruoyi.common.core.utils.StringUtils;
import com.ruoyi.common.core.web.domain.BaseEntity;
import com.ruoyi.common.datascope.annotation.DataScope;
import com.ruoyi.common.security.utils.SecurityUtils;
import com.ruoyi.system.api.domain.SysRole;
import com.ruoyi.system.api.domain.SysUser;
import com.ruoyi.system.api.model.LoginUser;

/**
 * 数据过滤处理
 * 
 * @author ruoyi
 */
@Aspect
@Component
public class DataScopeAspect
{
    /**
     * 全部数据权限
     */
    public static final String DATA_SCOPE_ALL = "1";

    /**
     * 自定数据权限
     */
    public static final String DATA_SCOPE_CUSTOM = "2";

    /**
     * 部门数据权限
     */
    public static final String DATA_SCOPE_DEPT = "3";

    /**
     * 部门及以下数据权限
     */
    public static final String DATA_SCOPE_DEPT_AND_CHILD = "4";

    /**
     * 仅本人数据权限
     */
    public static final String DATA_SCOPE_SELF = "5";

    /**
     * 数据权限过滤关键字
     */
    public static final String DATA_SCOPE = "dataScope";

    @Before("@annotation(controllerDataScope)")
    public void doBefore(JoinPoint point, DataScope controllerDataScope) throws Throwable
    {
        clearDataScope(point);
        handleDataScope(point, controllerDataScope);
    }

    protected void handleDataScope(final JoinPoint joinPoint, DataScope controllerDataScope)
    {
        // 获取当前的用户
        LoginUser loginUser = SecurityUtils.getLoginUser();
        if (StringUtils.isNotNull(loginUser))
        {
            SysUser currentUser = loginUser.getSysUser();
            // 如果是超级管理员，则不过滤数据
            if (StringUtils.isNotNull(currentUser) && !currentUser.isAdmin())
            {
                dataScopeFilter(joinPoint, currentUser, controllerDataScope.deptAlias(),
                        controllerDataScope.userAlias());
            }
        }
    }

    /**
     * 数据范围过滤
     * 
     * @param joinPoint 切点
     * @param user 用户
     * @param deptAlias 部门别名
     * @param userAlias 用户别名
     */
    public static void dataScopeFilter(JoinPoint joinPoint, SysUser user, String deptAlias, String userAlias)
    {
        StringBuilder sqlString = new StringBuilder();

        for (SysRole role : user.getRoles())
        {
            String dataScope = role.getDataScope();
            if (DATA_SCOPE_ALL.equals(dataScope))
            {
                //全部数据权限
                sqlString = new StringBuilder();
                break;
            }
            else if (DATA_SCOPE_CUSTOM.equals(dataScope))
            {
                //自定义数据权限
                sqlString.append(StringUtils.format(
                        " OR {}.dept_id IN ( SELECT dept_id FROM sys_role_dept WHERE role_id = {} ) ", deptAlias,
                        role.getRoleId()));
            }
            else if (DATA_SCOPE_DEPT.equals(dataScope))
            {
                //部门数据权限
                sqlString.append(StringUtils.format(" OR {}.dept_id = {} ", deptAlias, user.getDeptId()));
            }
            else if (DATA_SCOPE_DEPT_AND_CHILD.equals(dataScope))
            {
                //部门以及子部门数据权限
                sqlString.append(StringUtils.format(
                        " OR {}.dept_id IN ( SELECT dept_id FROM sys_dept WHERE dept_id = {} or find_in_set( {} , ancestors ) )",
                        deptAlias, user.getDeptId(), user.getDeptId()));
            }
            else if (DATA_SCOPE_SELF.equals(dataScope))
            {
                //本用户数据权限
                if (StringUtils.isNotBlank(userAlias))
                {
                    sqlString.append(StringUtils.format(" OR {}.user_id = {} ", userAlias, user.getUserId()));
                }
                else
                {
                    // 数据权限为仅本人且没有userAlias别名不查询任何数据
                    sqlString.append(" OR 1=0 ");
                }
            }
        }

        if (StringUtils.isNotBlank(sqlString.toString()))
        {
            Object params = joinPoint.getArgs()[0];
            if (StringUtils.isNotNull(params) && params instanceof BaseEntity)
            {
                BaseEntity baseEntity = (BaseEntity) params;
                baseEntity.getParams().put(DATA_SCOPE, " AND (" + sqlString.substring(4) + ")");
            }
        }
    }

    /**
     * 拼接权限sql前先清空params.dataScope参数防止注入
     */
    private void clearDataScope(final JoinPoint joinPoint)
    {
        Object params = joinPoint.getArgs()[0];
        if (StringUtils.isNotNull(params) && params instanceof BaseEntity)
        {
            BaseEntity baseEntity = (BaseEntity) params;
            baseEntity.getParams().put(DATA_SCOPE, "");
        }
    }
}
```



提示

仅实体继承`BaseEntity`才会进行处理，`SQL`语句会存放到`BaseEntity`对象中的`params`属性中，然后在`xml`中通过`${params.dataScope}`获取拼接后的语句。

```java
package com.ruoyi.common.core.web.domain;

import java.io.Serializable;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;
import com.fasterxml.jackson.annotation.JsonFormat;

/**
 * Entity基类
 * 
 * @author ruoyi
 */
public class BaseEntity implements Serializable
{
    private static final long serialVersionUID = 1L;

    /** 搜索值 */
    private String searchValue;

    /** 创建者 */
    private String createBy;

    /** 创建时间 */
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private Date createTime;

    /** 更新者 */
    private String updateBy;

    /** 更新时间 */
    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private Date updateTime;

    /** 备注 */
    private String remark;

    /** 请求参数 */
    private Map<String, Object> params;

    public String getSearchValue()
    {
        return searchValue;
    }

    public void setSearchValue(String searchValue)
    {
        this.searchValue = searchValue;
    }

    public String getCreateBy()
    {
        return createBy;
    }

    public void setCreateBy(String createBy)
    {
        this.createBy = createBy;
    }

    public Date getCreateTime()
    {
        return createTime;
    }

    public void setCreateTime(Date createTime)
    {
        this.createTime = createTime;
    }

    public String getUpdateBy()
    {
        return updateBy;
    }

    public void setUpdateBy(String updateBy)
    {
        this.updateBy = updateBy;
    }

    public Date getUpdateTime()
    {
        return updateTime;
    }

    public void setUpdateTime(Date updateTime)
    {
        this.updateTime = updateTime;
    }

    public String getRemark()
    {
        return remark;
    }

    public void setRemark(String remark)
    {
        this.remark = remark;
    }

    public Map<String, Object> getParams()
    {
        if (params == null)
        {
            params = new HashMap<>();
        }
        return params;
    }

    public void setParams(Map<String, Object> params)
    {
        this.params = params;
    }
}
```


