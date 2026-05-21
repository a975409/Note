
[官方dotnet-skills](https://github.com/dotnet/skills)

## 前言

你是否也曾被 AI 编程助手的“幻觉”搞得头疼？明明写的是 .NET 代码，它却给你甩来 Java 的解决方案；明明你在用 .NET 10 + Minimal API，它却在教你用 ASP.NET Web Forms 的写法。

现在 .NET 官方团队发布了 .NET Agent Skills（**dotnet/skills**），一套专为 AI 编码 Agent 打造的 .NET 官方技能库（Agent Skills）。它就像给 Copilot CLI、Claude Code、Codex CLI 等 AI 工具装上了"专业大脑"，让它们真正理解 .NET 的框架、API 、单元测试和最佳实践等，从此告别胡说八道，写出靠谱代码。

![](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/54370094bd66476d85ed74eb5debcbbe~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6L-96YCQ5pe25YWJ6ICF:q75.awebp?rk3s=f64ab15b&x-expires=1779804601&x-signature=%2FaO2cVxlKZvN0LFf8BQ0UnX%2BA6A%3D)

## Agent Skills 介绍

Agent Skills（智能体技能） 是一套将专业知识与工作流规范封装为可复用资产的标准化解决方案。

它本质上是一个模块化的 Markdown 文件，能够：

- 支持自动触发，无需手动输入提示词
- 支持团队共享，统一团队协作标准
- 支持工程化管理，纳入版本控制与 CI/CD 流程
- 教会 AI 工具（如 Claude、Copilot、Cursor 等）精准执行特定任务

从此，告别重复造轮子，让 AI 编程真正走向专业化、规模化。

![](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/b89dbd941abe467d9fbf5c5efbe8fd3b~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6L-96YCQ5pe25YWJ6ICF:q75.awebp?rk3s=f64ab15b&x-expires=1779804601&x-signature=mUY4vpdlN8KAG%2F5KojnKN8ttjSU%3D)

## .NET Agent Skills

目前 dotnet/skills 仓库已经涵盖了从核心开发到高性能诊断的多个领域插件：

|插件|描述|
|---|---|
|`dotnet`|用于处理常见 .NET 编码任务的核心 .NET 技能集合。|
|`dotnet-data`|用于 .NET 数据访问及 Entity Framework 相关任务的技能。|
|`dotnet-diag`|用于 .NET 性能调查、调试与事件分析的技能。|
|`dotnet-msbuild`|全面的 MSBuild 与 .NET 构建技能：故障诊断、性能优化、代码质量提升及现代化改造。|
|`dotnet-nuget`|NuGet 与 .NET 包管理技能：依赖项管理与项目现代化。|
|`dotnet-upgrade`|用于跨框架版本、语言特性及兼容性目标迁移和升级 .NET 项目的技能。|
|`dotnet-maui`|用于 .NET MAUI 开发的技能：环境配置、诊断与故障排查。|
|`dotnet-ai`|面向 .NET 的 AI/ML 技能：技术选型、LLM 集成、智能体工作流、RAG 流水线、MCP 及经典 ML 与 ML.NET 的结合应用。|
|`dotnet-template-engine`|.NET 模板引擎技能：模板发现、项目脚手架搭建与模板创作。|
|`dotnet-test`|用于运行、诊断与迁移 .NET 测试的技能：测试执行、筛选、平台检测及 MSTest 工作流。|
|`dotnet-aspnet`|ASP.NET Core Web 开发技能：中间件、端点、实时通信及 API 设计模式。|

## Claude Code 安装

1. 打开 Claude Code 终端
2. 添加市场
```bash
/plugin marketplace add dotnet/skills
```

![](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/f6797efd7a5b4023ae616a76e0a41d33~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6L-96YCQ5pe25YWJ6ICF:q75.awebp?rk3s=f64ab15b&x-expires=1779804601&x-signature=pFn9kB6M1SJBYjhv3RO7bAlooRo%3D)

3. 安装插件
```bash
/plugin install <plugin>@dotnet/skills 

# 安装用于处理常见 .NET 编码任务的核心 .NET 技能集合 /plugin install dotnet@dotnet/skills
```

![](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/83c219803463481b914ed76ddf5faed2~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6L-96YCQ5pe25YWJ6ICF:q75.awebp?rk3s=f64ab15b&x-expires=1779804601&x-signature=3HKt9VH9Hl5V12Vf4qTrqkgt1Wk%3D)

4. 重新启动 Claude Code 终端以载入新插件
5. 查看可用技能

```bash
/skills
```

![](https://p9-xtjj-sign.byteimg.com/tos-cn-i-73owjymdk6/c98a67f8c1db4d83ad72468df1a855a7~tplv-73owjymdk6-jj-mark-v1:0:0:0:0:5o6Y6YeR5oqA5pyv56S-5Yy6IEAg6L-96YCQ5pe25YWJ6ICF:q75.awebp?rk3s=f64ab15b&x-expires=1779804601&x-signature=pfkcRM13qsl%2FvsDBGw4IQwnJUcI%3D)

6. 接下来在你的 Claude Code 中验证安装 dotnet@dotnet/skills 插件后的 AI 输出效果吧
