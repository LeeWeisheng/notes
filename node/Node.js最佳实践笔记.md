# Node.js 最佳实践笔记

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [The Twelve-Factor App](https://12factor.net/)

## 1. 项目结构

- 组件化构建项目结构，即按功能划分文件和目录，而非按技术角色划分。

- 分层设计组件，将 web 层限制在一定边界之内，此边界通常为控制器。以 Koa 为例，应该将 `ctx`、`ctx.request`、`ctx.response` 等限制在 controller 内，避免将这些对象传递到 service 中。

- 封装公共模块，放到公共目录（如 libraries）中，或发布为 NPM 包。

- 将 API 声明与网络配置分离。如：将路由配置、中间件配置等放在 app.ts 文件中，将端口监听、服务器运行等放在 index.ts 文件中。

- 使用配置文件管理配置信息，根据运行环境的不同采用不同的配置文件，敏感信息不要提交到 Git 仓库中，可以使用 [dotenv](https://www.npmjs.com/package/dotenv)、[rc](https://www.npmjs.com/package/rc) 等工具来读取配置文件。

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

- 使用日志记录错误，例如 [pino](https://github.com/pinojs/pino)、[log4js](https://www.npmjs.com/package/log4js) 等。

- 使用测试框架测试错误的流向。

- 使用性能监控产品发现错误。

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
