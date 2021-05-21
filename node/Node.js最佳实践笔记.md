# Node.js 最佳实践笔记

[Node.js Best Practices.](https://github.com/goldbergyoni/nodebestpractices)

## 1. 项目结构实践

- 组件化构建项目结构，即按功能垂直划分文件和目录，而非按技术角色水平划分。

  ```
  ├── src
  │   ├── components
  │   │   └── users
  │   │       ├── controller.ts
  │   │       ├── entity.ts
  │   │       ├── service.ts
  │   │       └── test.ts
  │   │── libraries
  │   └── index.ts
  ├── package.json
  └── tsconfig.json
  ```

- 分层设计组件，将 Web 层（如 Koa）限制在控制器边界中。
