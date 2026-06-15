---
name: coding-standards
description: 编程规范与 Git 工作流标准。当进行代码编写、重构、Git 提交、分支创建/切换/合并时使用本规范。强制要求 Conventional Commits 格式、详细的提交信息、规范的分支命名，且禁止任何形式的 Co-Authored-By 或 AI 生成器署名。
---

# 编程规范与 Git 工作流

本规范适用于项目内所有代码编写、版本管理与协作流程。遇到与现有项目约定冲突时，以项目约定为准并向用户确认。

## 一、编程规范

### 1. 代码风格
- 遵循所在语言的官方风格指南：Rust 用 `rustfmt` + `clippy`，Python 用 PEP8 / `black`，Go 用 `gofmt`，JS/TS 用项目配置的 ESLint/Prettier
- 缩进、括号、引号风格全项目保持一致
- 软性上限：单文件 ≤ 500 行；函数 ≤ 50 行；嵌套 ≤ 4 层；超出时考虑拆分

### 2. 命名规范
- 变量/函数：按语言约定使用 `camelCase` 或 `snake_case`
- 常量：`UPPER_SNAKE_CASE`
- 类型/类/结构体：`PascalCase`
- 布尔变量以 `is` / `has` / `can` / `should` 开头
- 名字必须表达意图，禁止 `temp` / `data` / `info` / `value` / `tmp1` 等无意义命名

### 3. 注释规范
- 默认不写注释，仅在"为什么这样做"非显而易见时写
- 不写描述"做了什么"的注释——代码应能自解释
- 公共 API、对外接口必须有文档注释
- TODO 统一格式：`TODO(<负责人或issue>): <描述>`
- 禁止保留被注释掉的代码，删除即可

### 4. 错误处理
- 不忽略错误，不写空 `catch` / `unwrap()`（除非显式断言不可能失败）
- 业务错误用领域明确的错误类型，不要一律返回字符串或裸数字
- 只在系统边界（用户输入、外部 API、文件 IO）做校验
- 不为不可能发生的场景写防御性代码

### 5. 工程原则
- 单一职责：一个函数/模块只做一件事
- DRY：三处以上重复再考虑抽象，拒绝过早抽象
- 依赖倒置：面向接口而非实现
- 新增第三方依赖前评估必要性与维护活跃度
- 不提交无用的死代码、调试输出、注释块

## 二、Git 分支管理

### 1. 分支模型（基于 Git Flow 简化）

| 分支类型 | 命名格式 | 用途 | 从何处拉出 | 合并回 |
|----------|----------|------|------------|--------|
| 主干 | `main` | 生产环境稳定代码 | — | — |
| 开发 | `develop` | 集成与最新开发成果 | `main` | `main` |
| 功能 | `feature/<issue>-<desc>` | 新功能开发 | `develop` | `develop` |
| 修复 | `bugfix/<issue>-<desc>` | 非紧急缺陷修复 | `develop` | `develop` |
| 热修 | `hotfix/<issue>-<desc>` | 生产紧急修复 | `main` | `main` + `develop` |
| 发布 | `release/<version>` | 发布准备与验收 | `develop` | `main` + `develop` |

### 2. 分支命名规则
- 全小写，单词以连字符 `-` 分隔
- 必须带 issue/工单编号（无编号时用简短语义描述）
- 描述部分不超过 4 个单词，准确反映分支内容
- 禁止使用个人姓名、纯日期、`test`、`tmp`、`wip` 等无意义命名

✅ 正确：`feature/JIRA-123-user-login`、`bugfix/GH-456-null-pointer-crash`、`hotfix/JIRA-789-payment-timeout`
❌ 错误：`zhangsan-branch`、`test123`、`fix`、`20260615-update`、`tmp`

### 3. 分支生命周期
- feature/bugfix 分支应短命，生命周期建议 ≤ 1 周
- 合并后立即删除远端与本地分支
- 长期存活的分支仅允许 `main`、`develop`
- 活跃开发期间定期 `rebase` 或 `merge` develop，避免大幅偏离

### 4. 合并策略
- 默认使用 `--no-ff` 保留分支历史，便于追溯
- 合并前必须通过 CI 与代码审查
- 存在冲突时优先理解双方意图，不要机械覆盖

## 三、Git 提交规范

### 1. 提交信息格式（Conventional Commits）

```
<type>(<scope>): <subject>

<body>

<footer>
```

`scope` 可选，其余字段按下面规则执行。

### 2. type 取值

| type | 含义 | 示例场景 |
|------|------|----------|
| `feat` | 新功能 | 新增用户登录接口 |
| `fix` | 缺陷修复 | 修复支付超时问题 |
| `docs` | 文档变更 | 更新 README、API 文档 |
| `style` | 格式调整（不影响逻辑） | 代码格式化、去尾随空格 |
| `refactor` | 重构（非新增功能、非修复缺陷） | 提取公共函数、调整结构 |
| `perf` | 性能优化 | 减少数据库查询次数 |
| `test` | 测试相关 | 新增/补充单元测试 |
| `build` | 构建系统或依赖变更 | 升级依赖版本 |
| `ci` | CI 配置变更 | 修改 GitHub Actions |
| `chore` | 杂项（不修改源码或测试） | 更新 .gitignore |
| `revert` | 回滚某次提交 | revert: feat: 用户登录 |

### 3. subject 规则
- 中文用动宾结构，英文用祈使句现在时
- 不超过 50 个字符
- 结尾不加句号
- 准确描述本次提交"做了什么"

✅ `feat(auth): 新增基于 JWT 的用户登录`
❌ `更新了一些东西`、`fix bug`、`完成了今天的任务`、`update`

### 4. body 规则（必须详细）
- 重点解释"为什么"做这个改动，而非罗列代码改动
- 每行不超过 72 字符
- 用 `-` 项目符号列举关键变更点
- 必须覆盖：改动动机、主要变更、影响范围、注意事项或后续 TODO

### 5. footer 规则
- 破坏性变更：以 `BREAKING CHANGE:` 开头，列出影响与迁移指引
- 关联工单：`Closes #123`、`Fixes #456`、`Refs #789`

### 6. 完整示例

```
feat(auth): 新增基于 JWT 的用户登录接口

- 引入 jsonwebtoken crate 实现签发与校验
- 新增 /api/auth/login 与 /api/auth/refresh 两个端点
- 登录成功返回 access_token(15min) 与 refresh_token(7d)
- 密码使用 bcrypt(cost=12) 存储，禁止明文落库
- 新增 AuthMiddleware 自动校验 Authorization 头并注入用户上下文

旧的 session-cookie 方案保留两个版本后移除，迁移文档见 docs/auth-migration.md。

Closes JIRA-123
BREAKING CHANGE: /api/login 响应结构由 {session_id} 改为 {access_token, refresh_token}
```

### 7. 提交粒度
- 一次提交只做一件事（原子提交）
- 禁止一个提交混合多个不相关的变更
- 大功能拆分为多个小提交，每个都能独立编译通过、测试通过
- 禁止"巨型提交"——项目首次初始化除外

## 四、强制禁令

### 1. 禁止添加 Co-Authored-By 与 AI 生成器署名
所有 git 提交信息中**严禁**出现以下任何形式，无论提交是否由工具辅助完成：

```
Co-Authored-By: Claude <noreply@anthropic.com>
Co-Authored-By: <任何来源>
Generated with Claude Code
🤖 Generated with [Claude Code]
🤖 Generated with Claude Code
```

提交信息必须保持干净，仅体现项目本身与人类作者。

### 2. 禁止跳过 Git 钩子
除非用户明确要求，否则禁止使用：
- `--no-verify`（跳过 pre-commit / pre-push 钩子）
- `--no-gpg-sign` / `-c commit.gpgsign=false`（跳过签名）
- `--no-edit` 配合 `rebase`（不允许，非合法选项）

钩子失败时应当排查根因并修复，而非绕过。

### 3. 禁止破坏性操作
未获用户明确许可前，禁止：
- `git push --force` / `git push -f`（特别禁止对 `main` / `master` / `develop`）
- `git reset --hard`
- `git checkout .` / `git restore .` / `git clean -fd`
- `git branch -D` 删除他人分支
- `git commit --amend` 已推送到远端的提交
- `git rebase` 已推送的公共分支

### 4. 禁止提交以下内容
- 密钥、token、密码、连接字符串等敏感信息
- 构建产物（`target/`、`dist/`、`*.class`、二进制）
- IDE 个人配置（`.idea/`、`.vscode/` 中非共享部分）
- 临时文件、日志、缓存
- 超过 1MB 的二进制资源（应使用 LFS 或外部存储）
- 跨多个不相关模块的混合变更

## 五、提交前自检清单

每次提交前对照检查，全部通过后方可提交：

- [ ] 代码遵循项目风格指南，通过 lint 检查
- [ ] 无被注释掉的代码、无调试 `println!`/`console.log`
- [ ] 新增/修改的代码有对应测试，且本地通过
- [ ] 分支命名符合第二节规范
- [ ] 提交信息符合 Conventional Commits 格式
- [ ] 提交信息 body 详细说明了"为什么"和影响范围
- [ ] 本次提交是原子的，只做一件事
- [ ] 提交信息中**没有任何** `Co-Authored-By` 或生成器署名
- [ ] 未提交敏感信息与无关文件
- [ ] 未使用 `--no-verify` 等绕过手段
