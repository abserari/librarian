# 权限管理
---

GoAdmin已经内置了[RBAC](https://www.baidu.com/s?wd=rbac)权限控制模块。以超级管理员身份进入主页后，展开左侧边栏的管理，下面有用户、角色、权限三项的管理面板：

![rbac](http://quick.go-admin.cn/docs/rbac.png)

## 权限说明

权限管理是针对路由和方法进行限制，路由可以 **使用golang的正则匹配**。只要方法和路由能对应上，则权限验证通过。因此需要自己对各个路由和方法进行配置，从而自己决定权限设置的粒度。

关于某个表格的管理，假设表格名为```users```，相应的系统增删改查路由如下：

|  权限   | 路由  | 方法  | 
|  ----  | ----  | ----  |
| 列表页面 | /info/users | GET |
| 所有记录编辑页面 | /info/users/edit | GET |
| 指定记录编辑页面 | /info/users/edit?id=2 | GET |
| 所有记录编辑操作 | /edit/users | POST |
| 指定记录编辑操作 | /edit/users?id=2 | POST |
| 新增页面 | /info/users/new | GET |
| 新建操作 | /new/users | POST |
| 导出操作 | /export/users | POST |
| 所有权限 | * | (留空)

**注意1：路由的匹配规则说明**

可以看到上表中有```id=2```的参数，系统规定，路由中可带**路由参数**，只要待验证的路由中带有该参数且值匹配得上，则通过验证。而系统规定的主键参数，**__goadmin_edit_pk**， **__goadmin_detail_pk**，**__goadmin_detail_pk**，均可以用**id**来代替，比如：

> 待验证路由：/info/users/edit?__page=1&__pageSize=10&__sort=id&__sort_type=desc&__goadmin_edit_pk=3632
> 路由规则：/info/users/edit?id=3632
> 验证结果：通过

同时，如果是POST方法，需要对表单参数进行限制的话，同样也可以将该参数写在路由规则上，系统会判断是否在请求表单中带有该参数，如有的话，则认为权限验证正确。

**注意2：设置了权限并不意味着就可以登录或左侧菜单就可以看到了**

设置了权限，登录不成功或如果需要左侧菜单显示出来，你还需要在菜单编辑页面中设置 **对应的角色**。

**注意3：权限设置的高于在菜单配置的角色**

也就是说，假设菜单1，对应的角色是operator，而拥有所有权限的用户依然可以访问菜单1。

**注意4：UI组件的显示跟权限相关**

也就是说，如果你没有编辑的权限，则你将看不到编辑的按钮。