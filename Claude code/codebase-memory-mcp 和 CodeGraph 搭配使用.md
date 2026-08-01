
參考文件：
https://www.cnblogs.com/ccsvip/p/20788063

本文简单介绍如何快速使用 `codebase-memory-mcp` 和 `CodeGraph`，让 AI 编程助手更好地理解项目结构、调用链和代码影响范围。

## 一、它们分别解决什么问题？

简单来说：

- **CodeGraph**：适合日常编码时快速理解代码、查调用链、分析改动影响范围。
- **codebase-memory-mcp**：适合做更结构化的代码图谱分析，比如架构关系、函数依赖、Route / Service / Controller 关系、ADR 管理等。

推荐搭配方式：

> CodeGraph 作为日常默认入口，codebase-memory-mcp 作为高级图分析工具。

## 二、什么时候用 CodeGraph？

适合这些场景：

```text
这个接口是怎么走到数据库查询的？
这个函数被谁调用了？
我要改这个方法，会影响哪些地方？
帮我理解登录流程的完整调用链。
```

CodeGraph 的优势是使用简单，适合让 AI 快速拿到：

- 相关源码
- 调用路径
- 影响范围
- 上下文代码

日常开发中，优先让 AI 使用 CodeGraph。

## 三、什么时候用 codebase-memory-mcp？

适合这些场景：

```text
分析整个项目的架构关系。
查找所有 Route 和 Controller 的对应关系。
查询哪些函数调用最频繁。
分析哪些文件之间依赖最复杂。
记录或查询 ADR 架构决策。
```

它更适合做全局、结构化、图数据库类型的分析。

## 四、快速安装 CodeGraph

### 方式一：PowerShell 安装

```powershell
irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex
```

### 方式二：npm 安装

```bash
npm i -g @colbymchenry/codegraph
```

进入项目目录后初始化：
```bash
cd your-project
codegraph init
```

重新建立索引：
```shell
codegraph index
```

更新索引：
```shell
codegraph sync
```

启动 MCP：
```bash
codegraph serve --mcp
```

## 五、快速安装 codebase-memory-mcp

PowerShell 安装：

```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.ps1 -OutFile install.ps1
.\install.ps1
```

给项目建立索引：

```bash
codebase-memory-mcp cli index_repository '{"repo_path": "/path/to/repo"}'
```

开启自动索引：

```bash
codebase-memory-mcp config set auto_index true
```

## 六、MCP 配置示例(專案根目錄/.mcp.json)

可以在 MCP 配置里同时配置两个工具：

```json
{
  "mcpServers": {
    "codegraph": {
      "type": "stdio",
      "command": "codegraph",
      "args": ["serve", "--mcp"]
    },
    
    /*如果是採用全域安裝的話就不需要這段設定*/
    "codebase-memory-mcp": {
      "command": "/path/to/codebase-memory-mcp",
      "args": []
    }
  }
}
```

配置完成后，AI 编程助手就可以同时使用这两个 MCP 工具。

## 七、推荐使用规则

可以把下面这段放到项目的 `CLAUDE.md`、`AGENTS.md` 或 Cursor Rules 中：

```md
代码库探索规则：

1. 日常理解代码、定位实现、追踪调用链时，优先使用 CodeGraph。
2. 修改代码前，先用 CodeGraph 获取相关源码和影响范围。
3. 如果需要架构统计、图查询、ADR、跨模块关系分析，再使用 codebase-memory-mcp。
4. 如果两个工具结果不一致，以当前代码文件内容为准，并重新同步索引。
5. 不要在 CodeGraph 已经返回足够上下文时重复全仓库 grep。
```

## 八、实际使用流程

例如你想让 AI 修改登录逻辑，可以这样问：

```text
帮我修改登录逻辑。先分析从 /login 到数据库查询的完整链路，并告诉我改动可能影响哪些测试。
```

推荐流程：

1. 先用 CodeGraph 分析登录接口的调用链。
2. 查看相关 Controller、Service、Repository、Model。
3. 分析改动影响范围。
4. 如果涉及架构层面的依赖，再用 codebase-memory-mcp 做补充分析。
5. 修改完成后，再检查相关测试和调用方。

## 九、小项目和大项目怎么选？

### 小项目

只用 CodeGraph 就够了。

它简单、直接，适合日常开发中快速获取代码上下文。

### 中大型项目

建议 CodeGraph 和 codebase-memory-mcp 一起使用。

- CodeGraph：负责日常代码理解和调用链分析。
- codebase-memory-mcp：负责架构分析、图查询和长期代码知识管理。

## 十、总结

推荐的组合方式是：

```text
CodeGraph = 快速理解代码和调用链
codebase-memory-mcp = 结构化代码图谱和架构分析
```

日常开发优先使用 CodeGraph。

当你需要更深入的项目结构分析、依赖关系查询、架构决策记录时，再使用 codebase-memory-mcp。

这样既能保持使用简单，又能在复杂项目中获得更强的代码理解能力。