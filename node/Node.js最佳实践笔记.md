# 《Node.js 最佳实践》笔记

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [The Twelve-Factor App](https://12factor.net/)

## 1. 项目结构

- 组件化构建项目结构，即按功能划分文件和目录，而非按技术角色划分。

- 分层设计组件，将 web 层限制在一定边界之内，此边界通常为控制器。以 Koa 为例，应该将 `ctx`、`ctx.request`、`ctx.response` 等限制在 controller 内，避免将这些对象传递到 service 中。

- 封装公共模块，放到公共目录（如 libraries）中，或发布为 NPM 包。

- 将 API 声明与网络配置分离。如：将路由配置、中间件配置等放在 app.ts 文件中，将端口监听、服务器运行等放在 index.ts 文件中。

- 使用配置文件管理配置信息，根据运行环境的不同采用不同的配置文件，机密信息不要提交到 Git 仓库中，可以使用 [dotenv](https://www.npmjs.com/package/dotenv)、[rc](https://www.npmjs.com/package/rc) 等工具来读取配置文件。

```
├── src
│   ├── components
│   │   └── users
│   │       ├── controller.ts
│   │       ├── entity.ts
│   │       ├── service.ts
│   │       └── test.ts
│   ├── libraries
│   ├── middlewares
│   ├── app.ts
│   └── index.ts
├── .env
├── package.json
└── tsconfig.json
```

## 2. 错误处理

- 使用 Async-Await 和 Promise 处理异步错误，避免使用回调。

- 使用内建错误对象，或从内建错误对象派生其他错误对象，禁止 `throw` 字符串。

- 区分业务错误和程序错误，业务错误需要被拦截，遇到未知的程序错误可重启服务。

- 集中处理错误，而非在各个中间件中单独处理。

- 为错误编写文档，让接口调用者清楚返回的是什么错误，可以使用 Swagger 等工具。

- 当出现不可处理的错误时，可以优雅的退出服务，并使用 [forever](https://www.npmjs.com/package/forever)、[pm2](https://www.npmjs.com/package/pm2) 等工具进行重启。

- 使用日志记录错误，例如 [pino](https://github.com/pinojs/pino)、[log4js](https://www.npmjs.com/package/log4js)、[winston](https://github.com/winstonjs/winston)、[bunyan](https://github.com/trentm/node-bunyan) 等。

- 使用测试框架测试错误的流向。

- 使用性能监控产品发现错误和宕机时间。

- 处理未捕获的异常。

  ```js
  // 监听未捕获的 promise 异常，并重新抛出，交由下面的监听处理
  process.on("unhandledRejection", (reason) => {
    throw reason;
  });

  // 监听所有未捕获的异常
  process.on("uncaughtException", (error) => {
    // 进行处理
  });
  ```

- 使用 [ajv](https://www.npmjs.com/package/ajv)、[joi](https://www.npmjs.com/package/joi) 等工具验证请求数据，并抛出业务错误。

- 禁止在异步函数中 `return` 一个 promise，必须 `return await`。

## 3. 编码风格

- 使用 [ESLint](https://eslint.org/) 检查程序语法错误，使用 [prettier](https://www.npmjs.com/package/prettier) 等修正代码格式。

- 使用 Node.js 特定的插件：[eslint-plugin-node](https://www.npmjs.com/package/eslint-plugin-node)、[eslint-plugin-mocha](https://www.npmjs.com/package/eslint-plugin-mocha)、[eslint-plugin-security](https://www.npmjs.com/package/eslint-plugin-security) 等。

- 避免使用匿名方法。

- 命名变量和方法使用小驼峰；命名类使用大驼峰；命名常量使用大写和下划线。

- 禁止使用 `var`，使用 `let` 和 `const` 代替。

- 在文件顶部 `require`，而不是在函数内部。

- 开发公共模块时，使用文件夹而非单个文件。

- 使用全等操作符代替弱等操作符。

- 当不可避免使用回调时，使用箭头函数作为回调。

## 4. 测试和代码质量

- 至少要编写 API 测试。

- 测试用例的描述应该包含三个部分：模块、场景、期望。

- 测试代码应该可分为三个部分：模拟数据、执行动作、断言。不同部分之间用空格隔开。

- 避免全局的数据模拟，应该在具体测试中单独模拟数据。

- 使用 `npm audit` 等周期性的审查依赖。

- 在测试描述中使用关键字标记测试，例如 `#api`，并可通过测试框架进行筛选。

- 使用 [IstanbulJS](https://github.com/istanbuljs/istanbuljs)/[NYC](https://github.com/istanbuljs/nyc) 等生成测试覆盖率。

- 使用 `npm outdated`、[npm-check-updates](https://www.npmjs.com/package/npm-check-updates) 等检查过时的依赖。

- 使用与线上类似的环境进行端对端测试，如 [docker-compose](https://docs.docker.com/compose/)。

- 使用静态分析工具定期进行重构。

- 选择持续集成平台，如：[Jenkins](https://jenkins.io/)、[CircleCI](https://circleci.com/)、[TravisCI](https://travis-ci.org/)。

- 使用 [sinon](https://www.npmjs.com/package/sinon)、[node-mocks-http
  ](https://www.npmjs.com/package/node-mocks-http) 等工具隔离测试中间件。

## 5. 部署到生产环境

- 添加线上监控，并在出现异常的时候通知到处理人。

- 使用可聚合的智能日志系统。

- 将 CPU 密集型任务（例如 gzip、SSL）委托给反向代理。

- 使用 .npmrc 文件配置依赖源，使用 lock 文件锁住依赖版本。

- 使用 [pm2](https://www.npmjs.com/package/pm2)、[docker](https://www.docker.com/)、[Kubernetes](https://kubernetes.io/) 等工具保护进程并实现宕机重启。

- 使用集群利用所有的 CPU 内核。

- 创建运维系统，获取系统的运行状态。

- 使用性能监控产品发现错误和宕机时间。

- 从开始便使代码生产就绪。

- 监控并保护内存使用。

- 使用其他服务（例如 Nginx、CDN）管理前端静态资源。

- 保持应用无状态。

- 使用 `npm audit` 周期性检查有漏洞的依赖项。

- 用户发起请求时，生成一个跟踪 ID，且相关日志中记录此 ID。

- 生产环境下设置环境变量 `process.env.NODE_ENV` 为 `"production"`。

- 使部署原子化、自动化、零宕机。

- 使用长期维护（LTS）的 Node.js 版本。

- 日志的存储目标不应该在程序中硬编码，应该直接输出在 `stdout` 中，并由运行环境去指定存储目标。

- 生产环境下使用 `npm ci` 安装依赖包。

## 6. 安全

- 使用检测工具检测安全问题，例如 [eslint-plugin-security](https://github.com/nodesecurity/eslint-plugin-security)。

- 对于小型服务，可使用中间件限制并发数量，对于大型服务，可使用外部服务来限制。

- 把机密信息从配置文件中抽离出来，通过环境变量设置。

- 访问数据库时，通过 ORM/ODM 库，例如 [Sequelize](https://github.com/sequelize/sequelize)、[knex.js](https://github.com/knex/knex)、[Mongoose](https://github.com/Automattic/mongoose)、[typeorm](https://github.com/typeorm/typeorm)，防止查询注入。

- 通用安全实践：

  - 使用 HTTPS，可选择免费的平台 [Let's Encrypt](https://letsencrypt.org/)。

  - 使用恒定时间算法 [`crypto.timingSafeEqual(a, b)`](https://nodejs.org/api/crypto.html#crypto_crypto_timingsafeequal_a_b) 来比较秘钥值或 Hash 值。

  - 使用 [`crypto.randomBytes(size[, callback])`](https://nodejs.org/api/crypto.html#crypto_crypto_randombytes_size_callback) 方法生成随机字符串。

  - 保护用户的身份信息不被泄露。

  - 更多信息可访问 [OWASP](https://owasp.org/)。

- 使用 [koa-helmet](https://www.npmjs.com/package/koa-helmet) 设置 HTTP 响应头以增加安全性。

- 使用 `npm audit` 周期性检查有漏洞的依赖项。

- 密码或秘钥使用 [bcrypt](https://www.npmjs.com/package/bcrypt) 或 [scrypt](https://www.npmjs.com/package/scrypt-js) 进行加密。

- 转义 HTML、JS 和 CSS 输出。

- 使用 [jsonschema](https://www.npmjs.com/package/jsonschema)、[joi](https://www.npmjs.com/package/joi)、[validator.js](https://github.com/validatorjs/validator.js) 等验证请求参数输入。

- 使用 JWT 时，要支持黑名单。

- 使用 [koa-brute](https://www.npmjs.com/package/koa-brute)、[rate-limiter-flexible](https://www.npmjs.com/package/rate-limiter-flexible) 等限制每个用户允许的登录请求，防止暴力破解。策略：

  - 限制用户登录连续失败的次数。
  - 限制每个 IP 地址失败的次数。

- 使用非 root 用户运行 Node.js。

- 使用反向代理或中间件限制请求负载大小，以防止 DDOS 攻击。

- 避免使用 `eval`、`new Function()`、`setTimeout`、`setInterval` 动态执行 JavaScript 代码字符串。

- 使用验证工具，例如 [safe-regex](https://github.com/substack/safe-regex)，来验证正则表达式是否安全。

- 避免使用变量加载模块。

- 使用 [sandbox](https://www.npmjs.com/package/sandbox)、[vm2](https://www.npmjs.com/package/vm2) 等工具在沙箱中运行不安全的代码。
