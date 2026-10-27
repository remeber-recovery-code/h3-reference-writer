# H3 Reference Writer

一个按 **Writer 路线**编写 MiniMax H3 提示词的 Agent Skill。

你提供参考图片、视频、音频或素材的文字说明，以及剧情、对白、时长和比例。技能按照以下流程组织写作：

```text
逐项描述素材 → 固定引用编号 → 拼接参考说明 → 按官方 H3 格式写提示词
```

可用于 Codex，以及能够读取 `SKILL.md` 的其他智能体。素材的直接观察能力取决于运行环境；也可以提供已有的文字描述。技能本身只准备文字，不提交视频生成任务。

## 安装

```shell
npx skills add https://github.com/remeber-recovery-code/h3-reference-writer --skill h3-reference-writer
```

私有仓库需要当前 Git 环境具有对应的访问权限。也可以把本仓库放进智能体的技能目录，并保留其目录结构。

## 使用

在支持显式技能调用的环境中：

```text
用 $h3-reference-writer，根据这两张图写一个 10 秒、16:9 的 Ref2VA 提示词。
第一张参考人物身份，第二张只参考构图。
目标人物全程穿蓝色外套，说中文“早上好”。只输出提示词。
```

只整理素材说明：

```text
用 $h3-reference-writer，只描述这组参考素材并固定 Picture / Video / Audio 编号，先不写完整提示词。
```

也可要求导出给外部 LLM 使用的完整 `system` / `user` 消息。默认交付是素材编号对照和可复制的 H3 提示词；要求“只输出提示词”时仅交付提示词。

## 工作方式

- 区分源素材事实、用户提供的描述与目标改动，避免把参考图的差异直接带入目标画面。
- Picture、Video、Audio 各自编号；Subject 表示从素材提取的可复用内容。已有素材绑定保持一致。
- 支持 T2VA、I2VA、FL2VA、L2VA 和 Ref2VA；模式根据素材在目标中的用途确定。
- 最终正文按官方指南用英语写作，原对白、歌词和画内文字保留原语言。

## 官方格式指南

技能优先读取当前环境已安装的官方 `h3-prompt-writing`。没有安装时，可直接读取 [Base 指南](https://raw.githubusercontent.com/MiniMax-AI/MiniMax-H3/main/skills/h3-prompt-writing/references/base-en.txt)和 [Ref2VA 指南](https://raw.githubusercontent.com/MiniMax-AI/MiniMax-H3/main/skills/h3-prompt-writing/references/ref-en.txt)。

需要本地读取指南时，可以另外安装官方技能：

```shell
npx skills add https://github.com/MiniMax-AI/MiniMax-H3 --skill h3-prompt-writing
```

本仓库不分发官方指南副本。在线指南跟随官方 `main`；Writer 流程的源码核对版本固定在下方列出的提交。离线使用完整写作功能前，请准备好官方技能及对应指南。

## 文件

```text
SKILL.md                       技能入口与完整写作流程
agents/openai.yaml             Codex 界面元数据
references/writer-assembly.md  Writer 消息组装及编号示例
```

## 来源

流程提炼自 [MiniMax-H3-Prompt-Rewriter-ComfyUI](https://github.com/pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI) 的 Writer 路线，核对版本为 0.32.2，提交为 `cedd084f783bcb773648ba5ea3710d267bbc3261`。主要依据是 [universal.py 的素材描述与拼接逻辑](https://github.com/pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI/blob/cedd084f783bcb773648ba5ea3710d267bbc3261/minimax_h3_rewriter/universal.py#L861-L955)及 `guide_prompt.py` 的写作输入结构。

H3 输出格式依据 [MiniMax 官方 h3-prompt-writing](https://github.com/MiniMax-AI/MiniMax-H3/tree/main/skills/h3-prompt-writing)。这是面向对话智能体的写作流程适配，不是官方托管 H3-Context-IR 的实现。
