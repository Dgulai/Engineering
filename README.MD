<!--
 * @Author: Gulai
 * @Date: 2023-07-03 22:42:03
 * @LastEditTime: 2023-07-03 23:54:41
 * @Description: 笔记https://appjiz2zqrn2142.pc.xiaoe-tech.com/p/t_pc/course_pc_detail/video/v_638072cfe4b0fc5d120a24cf
 * @FilePath: \react-mono-repo\README.MD
-->

# 项目结构：Multi-repo/Mono-repo 的选择

## Multi-repo 每个项目都有自己独立的仓库，业务代码都是此结构，逻辑清晰，协同管理比较繁琐

## Mono-repo 可以很方便的协同管理不同独立的库的生命周期，相对应会有更高的操作复杂度（一个仓库下管理很多不同的库）

### 本项目测试使用 Mono-repo

#### 使用工具： pnpm，pnpm 相比其他工具的优势：依赖安装快，更规范（处理幽灵依赖问题），详情可多做了解

#### 项目搭建

1. 初始化项目

- `npm i -g pnpm`
- `pnpm init`
- 添加配置文件 pnpm-workspace.yaml，见文件内注释

2. 定义开发规范（eslint + prettier）

- 工具使用 lint 和 prettier. 包括代码规范、代码风格、提交信息的检查
- 安装: `pnpm i eslint -D -w` **特别注意这个-w,由于我们是 Mono-repo 的仓库，-w 是声明需要将依赖安装到哪个项目中去，只写-w 表明安装到更目录中**
- 初始化: `npx eslint --init`
- 安装 ts-eslint: `pnpm i -D -w @typescript-eslint/eslint-plugin`
- 安装 prettier: `pnpm i prettier -D -w`
- 添加 .prettierrc.json 文件
- 将 prettier 集成到 eslint 中，安装两个插件: `pnpm i eslint-config-prettier eslint-plugin-prettier -D -w`
  - eslint-config-prettier：覆盖 ESLint 本身的规则配置
  - eslint-plugin-prettier：用 Prettier 来接管修复代码即 eslint --fix
- 添加 lint 脚本测试 `"lint": "eslint --ext .ts,.jsx,.tsx --fix --quiet ./packages"`, --ext,处理 .ts,.jsx 等格式，--fix 修复，--quiet 不输出反馈 针对./packages 目录

3. commit 规范检查,使用 husky

- 安装 husky，用于拦截 commit 命令： `pnpm i husky -D -w`
- 初始化 `npx husky install`，
- 将 lint 命令添加到 precommit 钩子中: `npx husky add .husky/pre-commit "pnpm lint"`, 代码 commit 的时候会自动进行格式检查
- pnpm lint 会对代码全量检查，当项目复杂后执行速度可能比较慢，届时可以考虑使用 lint-staged，实现只对暂存区代码进行检查
- 通过 commitlint 对 git 提交信息进行检查，首先安装必要的库：`pnpm i commitlint @commitlint/cli @commitlint/config-conventional -D -w`
- 新建配置文件.commitlintrc.js
- 集成到 husky 中：`npx husky add .husky/commit-msg "npx --no-install commitlint -e $HUSKY_GIT_PARAMS"`,具体含义查看文档吧
- conventional 规范集意义：提交的类型: 摘要信息`<type>: <subject>`,常用的 type 值包括如下:
  - feat: 添加新功能
  - fix: 修复 Bug
  - chore: 一些不影响功能的更改
  - docs: 专指文档的修改
  - perf: 性能方面的优化
  - refactor: 代码重构
  - test: 添加一些测试代码等等
