---
name: h3-reference-writer
description: 按 Writer 路线把参考图片、视频、音频与用户要求写成 MiniMax H3 提示词。用于先逐项描述素材、固定引用编号、拼接 reference_assets，再按官方格式写提示词；也可只整理素材描述。只负责文字准备，不执行视频生成。
---

# H3 Reference Writer

采用“逐项观察素材 → 固定编号 → 拼接参考说明 → 按 H3 指南写作”的文字工作流。当前助手完成观察与写作即可，不要求安装 ComfyUI、下载改写模型或另行调用 LLM 服务。

## 输入与范围

- 使用用户提供的素材、素材说明、剧情/动作要求、对白、时长、比例和任务模式；已有项目约束与素材编号继续有效。
- 未指定模式时根据用途判断：纯文字用 T2VA；首帧、尾帧、首尾帧分别用 I2VA、L2VA、FL2VA；复用人物、场景、动作、运镜或声音等参考内容用 Ref2VA。附图不自动等于首帧。
- 非关键缺项采用与上下文一致的假设，在最终提示词外简短说明；缺失会改变素材身份、首尾帧关系或对白时才询问。等待时可先整理已知素材。
- 用户只要素材描述时，到参考说明块为止；要求完整提示词时继续写作。此 skill 只准备文字，不提交视频生成任务。

## 1. 逐项描述素材

- 建立素材与标签的对应关系，保留用户已有顺序；未指定时用提供顺序。排除明确关闭或不用的素材。来源文件名或附件标识留在编号对照中。
- 有查看/听取能力时实际检查素材。区分实际观察、用户文字说明和目标改动；无法读取时标明缺口，不根据文件名编造画面、声音或对白。
- 图片描述人物/物体、服装姿态、位置关系、环境、构图、光线和风格；视频描述已观察到的动作顺序、镜头变化及必要时间位置；音频描述实际听到的声音类型、音色语速、语言、环境声或音乐特征。按用途突出相关细节。
- 不把目标中尚未发生的动作写成源素材事实。人物、服装、背景或对白的目标改动放入用户写作要求，与源描述分开。
- 每项可以附加描述要求或替换观察问题：附加要求保留原描述任务；替换问题改变该项观察重点。均不得把未观察到的内容声称为事实。
- 视频抽样仅支持已检查部分的描述；看到了视频画面不等于听到了原音轨。

## 2. 固定编号并拼接

- Picture、Video、Audio 各自从 1 编号，跨类别不共享计数器。标签在参考说明、最终提示词及生成端素材顺序之间保持一致；已有实际绑定不自动重排。
- Subject 是从素材提取的可复用内容，不是新增源文件编号。一张图可提供多个 Subject，同一 Subject 可来自多份素材；按官方指南定义来源关系。
- 视频自带声音仅在任务明确使用/参考它时获得 Audio 标签；Video 与 Audio 的数字不代表天然配对，需记录共同来源。首次按仓库的生成端顺序规划新绑定时，启用的视频音轨在独立音频前；已存在的实际绑定优先。
- 每项拼成一行：`Picture 1: ...`、`Video 1: ...`、`Audio 1: ...`。将描述内部多余换行收为段内空格，保留不同素材间的换行。
- 参考说明是文字，不能代替素材本体。文字整理完成不代表素材已上传、接线或编码。

## 3. 按官方指南写作

完整 H3 提示词按官方 h3-prompt-writing 指南写作，按以下顺序定位：

1. 若环境技能清单提供 h3-prompt-writing，按清单路径读取其 `SKILL.md`，再从该技能目录读取指南。同级安装时目录通常为 `../h3-prompt-writing/`，不要假设所有环境均采用此布局。
2. 未安装配套技能时，在线读取下面的官方指南即可完成写作，不要求先安装其他技能。

- T2VA / I2VA / FL2VA / L2VA：本地 `references/base-en.txt`，或[官方 Base 指南](https://raw.githubusercontent.com/MiniMax-AI/MiniMax-H3/main/skills/h3-prompt-writing/references/base-en.txt)。
- Ref2VA：本地 `references/ref-en.txt`，或[官方 Ref2VA 指南](https://raw.githubusercontent.com/MiniMax-AI/MiniMax-H3/main/skills/h3-prompt-writing/references/ref-en.txt)；镜头、对白、说话人语法按需参照 Base 指南。

这里的本地路径相对于已定位的 h3-prompt-writing 技能目录；本 skill 不附带官方指南副本。

以完整指南规则、任务含义和输出约束为依据，结合用户要求及参考说明创作。需要复查或导出 system/user 消息时读取 [references/writer-assembly.md](references/writer-assembly.md)；正常写作无需展示内部消息。

- Base 模式依次输出 `integrated_multimodal_description`、`overall_soundscape`、`non_diegetic_music`；首尾帧对齐句按模式置于字段之前。
- Ref2VA 依次输出 `subject_definitions`、`summary`、`retention_analysis`、`detailed_description`、`overall_soundscape`、`non_diegetic_music`。
- 正文用英语，原对白、歌词和画内文字保留原语言及用户指定措辞。素材说明不能覆盖用户目标。
- 写具体动作、空间关系和声音发生点。单个连续动作不为凑格式强加切镜；动作量、对白和镜头时间与所选时长相符。
- 配套指南不可用时先完成素材描述与写作输入，明确指南缺失，不将凭记忆写出的近似格式当作已核对的最终交付。

## 4. 检查与交付

检查实际使用的素材可追溯、没有虚构引用，Subject 与源文件区分清楚，目标改动未混入源描述，视频音轨与独立音频未串号；再按指南检查字段、对齐句、镜头时间、说话人和对白标签。直接修正可确认的问题，结构检查不能证明未来生成画面一定符合描述。

默认给简短素材编号对照及一个可复制的最终 H3 提示词文本块。用户只要提示词时仅给最终块；只要素材整理时给参考说明块。内部消息和观察过程按需要展示，不强制全部输出。

## 来源

提炼自 pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI 的 Writer 路线，核对版本 0.32.2，提交 `cedd084f783bcb773648ba5ea3710d267bbc3261`。素材描述与拼接依据 [universal.py L861–955](https://github.com/pytraveler/MiniMax-H3-Prompt-Rewriter-ComfyUI/blob/cedd084f783bcb773648ba5ea3710d267bbc3261/minimax_h3_rewriter/universal.py#L861-L955)。这是对话写作适配，不声称复刻官方托管 Context-IR 或保证生成效果。
