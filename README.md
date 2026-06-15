# AI-program-guide

一份用于 Claude Code 的编程规范 Skill，覆盖代码风格、Git 分支管理与提交规范。

## 包含内容

`SKILL.md` 中定义了一个名为 `coding-standards` 的 Skill，触发场景包括代码编写、重构、Git 提交与分支管理，强制约束：

- **编程规范** — 代码风格、命名、注释、错误处理、工程原则
- **Git 分支管理** — Git Flow 简化模型、分支命名规则、生命周期、合并策略
- **Git 提交规范** — Conventional Commits 格式、type 取值、subject/body/footer 规则、提交粒度
- **强制禁令** — 禁止任何形式的 `Co-Authored-By` 或 AI 生成器署名、禁止 `--no-verify`、禁止破坏性 Git 操作、禁止提交敏感信息
- **提交前自检清单** — 10 项逐条核对

## 安装方法

Claude Code 加载 Skill 的约定：文件名必须为 `SKILL.md`，并放在 `<skills 根目录>/<skill 名>/` 下。本仓库中 `SKILL.md` 的 `name` 字段为 `coding-standards`，因此目标路径为 `<skills 根目录>/coding-standards/SKILL.md`。

### 用户级安装（所有项目生效，推荐）

```bash
mkdir -p ~/.claude/skills/coding-standards
cp SKILL.md ~/.claude/skills/coding-standards/SKILL.md
```

### 项目级安装（仅对单个项目生效）

在目标项目根目录执行：

```bash
mkdir -p .claude/skills/coding-standards
cp SKILL.md .claude/skills/coding-standards/SKILL.md
```

> 注意：`cp` 的源路径需要根据你下载或克隆的位置调整。例如克隆到本地后：
>
> ```bash
> git clone https://github.com/moecly/AI-program-guide.git
> mkdir -p ~/.claude/skills/coding-standards
> cp AI-program-guide/SKILL.md ~/.claude/skills/coding-standards/SKILL.md
> ```

## 验证安装

重启或新开 Claude Code 会话后，在对话中输入 `/skills` 或直接询问「列出可用的 skill」，应能看到 `coding-standards`。

触发方式：让 Claude 进行代码编写、Git 提交、分支操作时，会自动应用本规范；也可显式说「按编程规范 skill 提交」。

## 自定义

直接编辑 `SKILL.md` 修改规则。修改用户级副本（`~/.claude/skills/coding-standards/SKILL.md`）只影响自己，修改本仓库后推送可让协作者拉取到最新版本。
