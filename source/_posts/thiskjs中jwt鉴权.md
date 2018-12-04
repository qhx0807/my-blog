---
title: thiskjs中jwt鉴权，session保存用户信息
date: 2018-11-20 11:04:52
tags: [thinkjs, node, jwt]
categories: javascript
---

JWT 在 ThinkJS 中的实践, 使用 `think-session-jwt`
thinkjs 中提供了session 扩展，可以很方便的读写 `session` 

#### 修改 Adapter 配置文件 `src/config/adapter.js`

```javascript
const JWTSession = require('think-session-jwt')

exports.session = {
  type: 'jwt',
  common: {
    cookie: {
      name: 'thinkjs'
    }
  },
  jwt: {
    handle: JWTSession,
    secret: 'where amazing happens', // secret is reqired
    tokenType: 'header', // ['query', 'body', 'header', 'cookie'], 'cookie' is default
    tokenName: 'authorization', // 'jwt' is default
    sign: { expiresIn: 60 * 60 * 12 },
    verify: {},
    verifyCallback: any => {} // 验证失败的回调函数
  }
}
```

#### 用户登录种下 session `src/controller/login.js`

```javascript
module.exports = class extends Base {
  async indexAction () {
    const { username, password } = this.post()
    const user = this.model('user')
    const userInfo = await user.where({ name: username }).find()
    if (think.isEmpty(userInfo)) return this.fail('用户不存在')
    if (userInfo.password !== password) return this.fail('密码不正确')
    delete userInfo.password
    const token = await this.session('userInfo', userInfo)
    this.success({token: token, ...userInfo })
  }
}
```

#### 在其他 Action 中使用

```javascript
const userInfo = await this.session('userInfo')
if (userInfo) {
  // ...
}
```

