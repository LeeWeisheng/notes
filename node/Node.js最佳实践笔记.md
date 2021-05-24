# Node.js 最佳实践笔记

- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [The Twelve-Factor App](https://12factor.net/)

## 1. 项目结构

- 组件化构建项目结构，即按功能划分文件和目录，而非按技术角色划分。
- 分层设计组件，将 Web 层限制在一定边界之内，此边界通常为控制器。
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
