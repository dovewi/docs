# 类级权限

类级权限是一种比[ACL权限管理]({{ site.baseUrl }}/rest/guide/#security)更广泛的限制访问的安全特性.

## `requiresAuthentication`

如果你想限制某个类只给授权的用户访问, 你可以使用`requiresAuthentication`类级别权限. 例如你希望你的 **授权用户** 在你的应用中去 `find` 和 `get` 对象 和你的管理员拥有所有权限, 你可以设置CLP:

```
// PUT http://localhost:1337/schemas/:className
// Set the X-Parse-Application-Id and X-Parse-Master-Key header
// body:
{
  classLevelPermissions:
  {
    "find": {
      "requiresAuthentication": true,
      "role:admin": true
    },
    "get": {
      "requiresAuthentication": true,
      "role:admin": true
    },
    "create": { "role:admin": true },
    "update": { "role:admin": true },
    "delete": { "role:admin": true },
  }
}
```

请注意，如果你允许任何人登陆你的服务器,这绝不会保护您的内容,任何用户都可以查询这个对象. 
