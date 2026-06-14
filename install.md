# 安装 Publia Skills

Publia Skills 是给 AI 助手使用的说明文件。安装后，AI 助手会知道怎样调用 `publia` 命令，把网页、Markdown 和图片上传到 Publia，并把预览链接返回给你。

## 1. 安装 Publia CLI

先检查本机是否已经有 `publia` 命令：

```bash
publia --help
```

如果还没有安装，可以从 GitHub 安装：

```bash
npm install -g publia-cli
```

安装后确认命令可用：

```bash
publia --help
publia commands --json
```

## 2. 登录 Publia

普通使用时，直接用浏览器登录：

```bash
publia auth login
publia auth whoami --json
```

如果你希望 AI 助手在无法打开浏览器的环境里使用 Publia，可以在 Publia 网页的 设置 → 开发者 页面签发 Token，然后设置环境变量。
服务地址默认已经是 `https://publia.ai`，通常不需要设置 `PUBLIA_URL`。

```bash
export PUBLIA_TOKEN=publia_xxx
publia auth whoami --json
```

每个账号只能保留一个 CLI Token。重新签发 Token 后，旧 Token 会立即失效。

## 3. 安装 Skills

使用 skills installer 安装：

```bash
npx skills add getpublia/skills
```

如果你的工具需要指定类型，可以这样写：

```bash
npx skills add getpublia/skills -a codex
npx skills add getpublia/skills -a claude-code
```

也可以安装为全局 skill：

```bash
npx skills add getpublia/skills -g
```

安装后查看列表：

```bash
npx skills list
# 应该能看到 publia
```

安装完成后，重启你的 AI 助手会话，让它读取新的 Skill。

## 4. 验证上传

可以用一个小目录测试上传：

```bash
publia upload ./output-site --to /agent-output --json
publia open /agent-output/output-site --json
```

上传成功后，返回结果里会包含可以访问的 URL。

## 手动安装

如果 skills installer 不可用，也可以手动安装：

```bash
git clone https://github.com/getpublia/skills ~/.publia-skills
mkdir -p ~/.codex/skills
ln -sfn ~/.publia-skills/skills/publia ~/.codex/skills/publia
```

更新时进入仓库执行：

```bash
cd ~/.publia-skills && git pull
```
