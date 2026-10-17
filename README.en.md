# SKILL of Three-Pass Paper Reading

**An evidence-focused research-paper reading skill with explicit statistical interpretation rules.**

[中文](README.md) · English (this page) · [English instructions](SKILL.en.md) · [中文指令](SKILL.md) · [Worked example](examples/wind-aware-rl-reading.en.md) · [MIT License](LICENSE)

Read a paper at the depth your goal requires: establish the big picture, trace claims to evidence, and evaluate what the conclusions actually support. Assess **validity, importance, and applicability separately** rather than reducing a paper to a single score.

## What this skill emphasizes

- **Evidence boundaries:** distinguish author claims, reported evidence, reader inference, and unresolved questions. Locate important numbers and judgments by page, section, figure, or table.
- **Statistical interpretation:** separate statistical significance from practical importance, association from causation, and absolute from relative changes. Check metric definitions and denominators before repeating headline results.
- **Proportionate criticism:** missing reporting is not proof that a procedure was not performed. Label suspected problems and verified problems differently.
- **Calibrated conclusions:** explain the paper's strongest evidence, main limitation, and what it does not establish; rewrite its conclusion at the strength supported by the evidence.
- **Purpose-driven depth:** stop after screening when that is sufficient; complete all three passes when the user explicitly requests a full close reading.

The three-pass method, Five Cs, evidence tracking, and reading-note templates are shared with other reading workflows. This project does not claim to invent them, and comparative performance against other skills has not been benchmarked.

## Installation

Prerequisites: Git and an assistant that supports local Skills. The commands below install into Codex's user-level skill directory. They do not require a GitHub token, API key, Python, or npm. Your assistant supplies the model and the tools needed to access papers.

The project title is **SKILL of Three-Pass Paper Reading**. Its repository name is `SKILL-of-Three-Pass-Paper-Reading`; its installation directory and invocation identifier remain `three-pass-paper-reading`.

### Windows: PowerShell

Copy and run:

```powershell
$codexHomePath = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME '.codex' }
$skillRootPath = Join-Path $codexHomePath 'skills'
New-Item -ItemType Directory -Path $skillRootPath -Force | Out-Null
git clone https://github.com/Chenghao-Huang1218/SKILL-of-Three-Pass-Paper-Reading.git (Join-Path $skillRootPath 'three-pass-paper-reading')
```

### macOS / Linux: Bash or Zsh

Copy and run:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/Chenghao-Huang1218/SKILL-of-Three-Pass-Paper-Reading.git "${CODEX_HOME:-$HOME/.codex}/skills/three-pass-paper-reading"
```

If the destination already exists, Git will refuse to overwrite it. Check whether it contains a previous manual installation or your own changes before proceeding. For a Git-cloned installation, update with `git -C <installation-directory> pull --ff-only`; this command does not apply to a directory installed by copying files.

After installing, start a new Codex chat and explicitly invoke `$three-pass-paper-reading`. If it is not discovered, verify that `SKILL.md` is directly inside the installed folder and that your assistant supports that skill location. For other assistants, use their documented Skills directory rather than assuming the Codex path applies.

### Without Git

Open the [repository](https://github.com/Chenghao-Huang1218/SKILL-of-Three-Pass-Paper-Reading), choose **Code → Download ZIP**, and extract it. Rename the project folder to `three-pass-paper-reading` and place it in your user-level Codex `skills` directory. Avoid an extra nested project folder.

The skill files should be arranged as follows; README and license files are omitted here:

```text
three-pass-paper-reading/
├── SKILL.md
├── SKILL.en.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── reading-note.md
│   ├── reading-note.en.md
│   ├── source-notes.md
│   └── source-notes.en.md
└── examples/
    ├── wind-aware-rl-reading.md
    └── wind-aware-rl-reading.en.md
```

## Usage

English and Chinese instructions, templates, and full examples are included. `SKILL.md` remains the discovery entry and routes English-language tasks to [SKILL.en.md](SKILL.en.md). No second installation or file replacement is needed. Output follows the requested language, or otherwise the language of the request. You can explicitly include **Respond in English** when desired.

Attach a paper PDF or provide an accessible full-text link, then send:

```text
Use $three-pass-paper-reading to closely read the attached paper.
Respond in English. Complete all three passes, explain the methods,
check the main claims against figures and tables, and separately assess
validity, importance, and applicability to my research.
Give source locations for important numbers and judgments.
```

For a quick screening:

```text
Use $three-pass-paper-reading for Pass 1 only. Respond in English.
Give a two-sentence overview, the Five Cs, and a reasoned recommendation
on whether to continue reading.
```

Natural-language requests can also work if your assistant automatically matches skills; explicitly naming the identifier is more direct.

## Worked example: wind-aware reinforcement learning

The example uses Abdullah Al Tasim and Wei Sun's *Wind-Aware Reinforcement Learning Control of a Small Quadrotor Using Learned Onboard Wind Estimation in Simulated Atmospheric Turbulence*, arXiv:2607.01528v1 (2026).

[Paper](https://arxiv.org/abs/2607.01528v1) · [PDF](https://arxiv.org/pdf/2607.01528v1) · [Full English example](examples/wind-aware-rl-reading.en.md) · [中文示例](examples/wind-aware-rl-reading.md)

Download or attach the paper and try:

```text
Use $three-pass-paper-reading to closely read:
https://arxiv.org/abs/2607.01528v1

Respond in English. My research concerns aerial manipulation.
Complete all three passes. In particular:
1. Explain the problem, Five Cs, and estimator–controller architecture.
2. Check the definitions of wind-estimation error, tracking improvement,
   and the wind-channel ablation.
3. Separately assess validity, importance, and applicability.
Locate key claims in the paper and mark anything you cannot verify.
```

The worked example demonstrates why the qualifications matter:

| Headline result | Qualification from close reading |
| --- | --- |
| Wind-estimation RMSE of 0.40 m/s | This is a per-flight averaged metric; the per-window vector RMSE is 1.05 m/s. [p.11, Table 1] |
| 48% lower horizontal tracking error | This compares the complete residual-control system with the specified PD baseline, not the wind-estimation module alone. [pp.19–21, Tables 4–5] |
| Wind perception accounts for roughly half the gain | This comes from zeroing the wind inputs of the same trained policy, not independently retraining a wind-blind policy. [p.18; pp.20–21] |
| Better behavior under out-of-distribution winds | The evidence concerns a finite simulation evaluation with a capped wind distribution; it is not a stability proof. [pp.23–27] |

These observations summarize the supplied paper and its worked reading, not a comparative benchmark of this skill. Outputs depend on the assistant, accessible materials, and prompt. Verify the paper before citing it.

## The three passes

| Pass | Purpose | Deliverable |
| --- | --- | --- |
| 1: Orientation | Understand the gist and decide whether to continue | Two-sentence summary, Five Cs, relevance, and next step |
| 2: Evidence | Follow the methods and argument | Method explanation, figure/table checks, claim–evidence mapping, revised summary |
| 3: Appraisal | Challenge the conditions behind the conclusion | Alternative explanations, strengths and limitations, three-part assessment, calibrated conclusion |

A reusable [English reading-note template](references/reading-note.en.md) is included, alongside the [Chinese version](references/reading-note.md).

## Scope and limitations

This is a set of instructions and reference templates for an existing assistant, not a standalone model or experiment runner. It does not bundle a PDF parser or PDF/HTML report renderer, and it does not guarantee correct statistical judgments.

The assistant needs full-text access and the ability to inspect relevant figures. Abstract-only access must not be presented as a full-paper review. Reading analysis, running code, reproducing experiments, and conducting a systematic review are distinct activities and should be reported separately.

## Sources and attribution

This skill was directly adapted from Benita Olivier's guide, *How to Read a Research Paper: A three-pass method for understanding and questioning research*. The guide attributes the three-pass method and Five Cs to S. Keshav's *How to read a paper* (2007, [DOI](https://doi.org/10.1145/1273445.1273458)).

See the [English source notes](references/source-notes.en.md) or [Chinese source notes](references/source-notes.md) for the distinction between source guidance, AI-workflow additions, and tightened statistical wording. Neither the guide PDF nor the research-paper PDF is redistributed. Their authors do not endorse this project merely because their work is discussed here.

## License

Original skill instructions, templates, and project documentation are available under the [MIT License](LICENSE). Referenced papers and guides remain subject to their respective rights; this license does not grant rights to those original materials.
