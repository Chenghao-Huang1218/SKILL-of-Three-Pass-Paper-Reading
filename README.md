# SKILL of Three-Pass Paper Reading

**三遍阅读法：强调证据边界与统计解释的论文阅读 Skill**

中文说明（当前页面） · [English](README.en.md) · [使用示例](examples/wind-aware-rl-reading.md) · [MIT License](LICENSE)

一个强调**证据边界与统计解释**的科研论文阅读 Skill：帮助 AI 追踪论文主张的依据，分别判断结果是否可信、是否重要、是否适用于当前问题，并形成与证据强度匹配的结论。

适合论文筛选、精读、组会准备和复现前评估。默认中文解释，保留关键英文术语。

## 为什么使用它

读懂方法和记住结果之后，还需要回答：**要相信这篇论文的结论，哪些条件必须成立？**

本 Skill 将这个问题落实到阅读过程：

- **分开评价有效性、重要性与适用性**：研究可信，不等于效应足够重要，也不等于适合你的课题。
- **明确统计解释边界**：区分显著性与实际意义、关联与因果、绝对与相对变化，以及不同指标的计算口径。
- **让批判也受证据约束**：未报告不等于没做；待验证问题不能直接写成研究缺陷。
- **形成可以准确复述的结论**：追踪“主张—方法—证据—边界”，最后改写出与证据相称的结论。
- **按目标控制深度**：速读可以停在第一遍；用户明确要求精读时连续完成三遍。

三遍阅读、五个 C、证据定位和结构化笔记也是其他论文阅读 Skill 使用的做法。本项目不声称首创这些方法，也尚未通过跨 Skill 对照实验验证效果优越性。

## 安装

需要先安装 Git，并使用支持本地 Skills 的助手。下面的命令将公开仓库克隆到 Codex 的用户级技能目录，不需要 GitHub 令牌、API Key、Python 或 npm；读取论文所需的模型及工具由助手提供。

项目名称为 **SKILL of Three-Pass Paper Reading**；GitHub 仓库名为 `SKILL-of-Three-Pass-Paper-Reading`。安装目录和调用标识仍为 `three-pass-paper-reading`。

### Windows（PowerShell）

打开 PowerShell，复制执行：

```powershell
$codexHomePath = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME '.codex' }
$skillRootPath = Join-Path $codexHomePath 'skills'
New-Item -ItemType Directory -Path $skillRootPath -Force | Out-Null
git clone https://github.com/Chenghao-Huang1218/SKILL-of-Three-Pass-Paper-Reading.git (Join-Path $skillRootPath 'three-pass-paper-reading')
```

### macOS / Linux（Bash 或 Zsh）

打开终端，复制执行：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/Chenghao-Huang1218/SKILL-of-Three-Pass-Paper-Reading.git "${CODEX_HOME:-$HOME/.codex}/skills/three-pass-paper-reading"
```

如果目标目录已经存在，Git 会拒绝覆盖。请先确认它是否是你先前手动安装或修改过的版本，不要直接删除。通过 Git 克隆安装的版本，可以使用 `git -C <安装目录> pull --ff-only` 更新；手动复制安装的版本不适用这条更新命令。

安装后新开一个 Codex 聊天，再显式调用 `$three-pass-paper-reading`。如果仍无法识别，检查实际技能目录中是否直接包含 `SKILL.md`，以及助手是否支持该技能目录。其他助手请使用其支持的 Skills 目录，不能假定 Codex 路径通用。

### 不使用 Git

在 [仓库页面](https://github.com/Chenghao-Huang1218/SKILL-of-Three-Pass-Paper-Reading) 选择 **Code → Download ZIP**。解压后，将项目文件夹改名为 `three-pass-paper-reading`，放入 Codex 用户级 `skills` 目录；不要额外嵌套一层项目文件夹。

目录结构为（文档和许可证文件略）：

```text
three-pass-paper-reading/
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── reading-note.md
│   └── source-notes.md
└── examples/
    └── wind-aware-rl-reading.md
```

本 Skill 不绑定特定检索产品，也不附带 PDF 解析器。助手需要具备读取论文全文及查看相关图表的能力。若无法访问全文，应明确限制，不能把摘要分析称为全文精读。

## 使用

安装并被助手发现后，可以这样调用：

```text
请使用 $three-pass-paper-reading 帮我精读附件论文。
我的研究方向是空中操作，请重点核对方法、实验比较和结论边界，
最后判断哪些思想可以迁移到我的研究。
```

也可以自然表达“请用三遍阅读法帮我读这篇论文”；具体是否自动匹配，取决于助手的技能发现机制。显式指定技能标识更明确。

Skill 指令和现有完整示例以中文编写；需要英文输出时，在请求中明确写出 `Respond in English`。英文安装和功能说明见 [English README](README.en.md)。

只需要筛选时：

```text
请使用 $three-pass-paper-reading 对附件论文只做第一遍阅读，
给出两句话概述、五个 C，以及是否值得继续精读的理由。
```

## 一个真实使用例子：风感知强化学习控制

示例论文：Abdullah Al Tasim 与 Wei Sun，*Wind-Aware Reinforcement Learning Control of a Small Quadrotor Using Learned Onboard Wind Estimation in Simulated Atmospheric Turbulence*，arXiv:2607.01528v1（2026）。

[论文页面](https://arxiv.org/abs/2607.01528v1) · [原文 PDF](https://arxiv.org/pdf/2607.01528v1) · **[完整三遍阅读示例](examples/wind-aware-rl-reading.md)**

读者可自行获取 PDF，上传给助手后输入：

```text
请使用 $three-pass-paper-reading 精读这篇论文：
https://arxiv.org/abs/2607.01528v1

我的研究方向是空中机械臂与空中操作。请完成三遍阅读：
1. 说明研究问题、五个 C 和方法主线。
2. 核对风估计误差、轨迹跟踪改善和风输入消融的指标口径。
3. 分别评价有效性、重要性、适用性，给出有证据边界的结论。
关键数值和判断请标注原文页码；无法核实的内容明确说明。
```

示例展示了怎样把摘要里的结果读准确：

| 看到的结果 | 阅读后需要补上的限定 |
| --- | --- |
| 风估计 RMSE 为 0.40 m/s | 这是整次飞行平均后的指标；逐窗口向量 RMSE 为 1.05 m/s。【p.11，Table 1】 |
| 水平跟踪误差降低 48% | 是完整残差控制系统相对本文 PD 基线的总体改善，不能全部归于风感知。【pp.19–21，Tables 4–5】 |
| 风感知贡献约一半收益 | 来自对同一已训练策略的风通道置零消融；未独立重训无风策略。【p.18；pp.20–21】 |
| 在分布外强风中表现更好 | 结论受指定仿真、有限回合及风速截断约束，不构成稳定性证明。【pp.23–27】 |

以上是基于实际精读整理的示例，并非跨模型性能基准。不同助手、材料访问范围和提示会影响输出；读者仍应核对原文。

## 三遍分别产出什么

| 阶段 | 阅读目的 | 主要产出 |
| --- | --- | --- |
| 第一遍 | 获得全貌、决定是否深入 | 两句话概述、五个 C、相关性与下一步 |
| 第二遍 | 理解方法、追踪论证 | 方法解释、图表核查、主张—证据表、修订概要 |
| 第三遍 | 检查结论成立的条件 | 替代解释、主要优点与局限、三维评价、结论改写 |

需要长期积累笔记时，使用 [阅读笔记模板](references/reading-note.md)。

## 当前能力与边界

这是由自然语言指令和参考模板组成的 Skill。它指导已有助手工作，不包含独立阅读模型或实验执行系统。

它没有附带自动 PDF/HTML 报告渲染器，也不保证助手每次都能正确识别统计问题。阅读分析不等于运行代码、复现实验或完成系统综述；这些状态应分别说明。

## 方法来源

本项目直接改编自 Benita Olivier 的 *How to Read a Research Paper: A three-pass method for understanding and questioning research* 指南。该指南将三遍阅读法和五个 C 归于 S. Keshav 的 *How to read a paper*（2007，[DOI](https://doi.org/10.1145/1273445.1273458)）。

AI 阅读的操作规则、统计解释收紧及不同研究类型的检查项，详见 [来源与改编边界](references/source-notes.md)。本仓库不附带原指南 PDF，也不附带示例研究论文 PDF；示例是阅读分析，不是论文作者对本项目的背书。

## 许可证

本仓库原创的 Skill 指令、模板和说明采用 [MIT License](LICENSE)。引用的论文、原指南及其内容仍归各自权利人所有；本项目许可证不授予这些原始材料的权利。
