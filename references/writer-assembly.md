# Writer 输入组装

需要检查素材说明如何进入写作，或用户要求导出外部 LLM 消息时读取。以下是虚构结构示例，不是实际素材观察记录。

## 系统消息

按顺序组织写作角色、当前模式的完整官方指南、任务含义、输出字段与顺序、首尾帧对齐或 Ref2VA 引用规则、输出语言与原对白保留要求。

完整指南从已安装的官方 h3-prompt-writing 技能读取；未安装时使用入口 SKILL.md 中列出的官方在线指南。本 skill 不重复保存官方指南。导出给外部模型的完整 system 时，实际展开所需指南，不能留下“在此放入指南”的占位符。

源码中的自定义 `system_prompt` 会整体替换默认 system，并非追加；仅在用户要求兼容导出或明确采用该替换语义时照做。日常写作的额外要求应与指南共同应用。

## 用户消息

```text
task: Ref2VA
resolution: 16:9
duration: 10s
reference_assets:
Picture 1: A short-haired woman in a blue jacket sits beside a café window.
Video 1: A slow forward camera move from the entrance toward a table.
Audio 1: A woman speaks gently at a measured pace.
original_prompt: 保留参考人物的外貌和蓝色外套，参考视频的缓慢推进，让她转向镜头说“早上好”。
```

Ref2VA 使用 `reference_assets`，首尾帧模式使用 `reference_material`。T2VA 从文字构建，不携带参考块。没有参考资料时不虚构 Ref2VA 引用。

用户原始要求留在 `original_prompt`，源素材说明留在参考块。人物设定图不自动成为精确首帧。

## 编号与生成端绑定

素材顺序为人物图 A、运镜视频 B、场景图 C、独立声音 D，且无已有绑定、未使用 B 原音轨时，对应 Picture 1、Video 1、Picture 2、Audio 1。

如果还使用 B 原音轨，首次按仓库音频顺序规划为：B 的启用音轨对应 Audio 1，D 对应 Audio 2。来源关系单独记录；不因 Video 与 Audio 数字相同就推断配对。

参考文本不是上传 API。生成端仍需接收按绑定顺序提供的真实素材；仅要求写作时不代替用户构建 ComfyUI 运行包或提交生成任务。

## 源码对应

- 排序与素材用途：[universal.py L251–282](https://github.com/pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI/blob/cedd084f783bcb773648ba5ea3710d267bbc3261/minimax_h3_rewriter/universal.py#L251-L282)。
- 逐项描述与参考块拼接：[universal.py L861–955](https://github.com/pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI/blob/cedd084f783bcb773648ba5ea3710d267bbc3261/minimax_h3_rewriter/universal.py#L861-L955)。
- 官方指南与 system/user 结构：[guide_prompt.py](https://github.com/pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI/blob/cedd084f783bcb773648ba5ea3710d267bbc3261/minimax_h3_rewriter/guide_prompt.py)。
- 指南与 Writer 调用入口：[nodes.py L1522–1552](https://github.com/pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI/blob/cedd084f783bcb773648ba5ea3710d267bbc3261/minimax_h3_rewriter/nodes.py#L1522-L1552)。

本 skill 的对话适配包括：当前助手完成观察和写作，默认仅交付编号对照与提示词，区分观察事实、用户说明及目标改动。它们是使用约定，不是源码的自动质量保证。
