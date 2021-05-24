# Node.js 最佳实践笔记

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [The Twelve-Factor App](https://12factor.net/)

## 1. 项目结构

- 组件化构建项目结构，即按功能划分文件和目录，而非按技术角色划分。

- 分层设计组件，将 web 层限制在一定边界之内，此边界通常为控制器。

- 封装公共模块，放到公共目录（如 libraries）中，或发布为 npm 包。

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

- 使用内建错误对象，或从内建错误对象派生其他错误对象，禁止抛出字符串错误。

- 区分业务错误和程序错误，业务错误需要被处理，程序错误可重启服务。

- 集中处理错误，而非在各个中间件中处理。

- 为错误编写文档，让接口调用者清楚返回的是什么错误，可以使用 Swagger 等工具。

- 当出现不可处理的错误时，可以优雅的退出服务，并使用 [forever](https://www.npmjs.com/package/forever)、[pm2](https://www.npmjs.com/package/pm2) 等工具进行重启。

- 使用日志记录错误，例如 [pino](https://github.com/pinojs/pino)、[log4js](https://www.npmjs.com/package/log4js) 等。

- 使用测试框架测试错误的流向。

- 使用性能监控产品（APM）发现错误和宕机时间。

- 处理未捕获的异常。

  ```js
  // 监听未 catch 的 promise 异常，并重新抛出，交由下面的程序处理
  process.on("unhandledRejection", (reason) => {
    throw reason;
  });

  // 监听未捕获的异常
  process.on("uncaughtException", (error) => {
    // 进行处理
  });
  ```

- 使用 [ajv](https://www.npmjs.com/package/ajv)、[joi](https://www.npmjs.com/package/joi) 等工具验证请求数据。

- 禁止在异步函数中 `return` 一个 Promise。

## 3. 编码风格

- 使用 [ESLint](https://eslint.org/) 检查程序语法错误，使用 [Prettier](https://www.npmjs.com/package/prettier) 修正代码格式。

- 使用 Node.js 特定的插件：[eslint-plugin-node](https://www.npmjs.com/package/eslint-plugin-node)、[eslint-plugin-mocha](https://www.npmjs.com/package/eslint-plugin-mocha)、[eslint-plugin-security](https://www.npmjs.com/package/eslint-plugin-security) 等。

- 避免匿名方法。

- 命名变量和方法使用小驼峰；命名类使用大驼峰；命名常量使用大写和下划线。

- 禁止使用 `var`，使用 `let` 和 `const` 代替。

- 在文件顶部 `require`，而不是在函数内部。

- 开发模块时，使用文件夹而非单个文件。

- 使用全等操作符代替弱等操作符。

- 当不可避免使用回调时，使用箭头函数作为回调。
