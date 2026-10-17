# Generate a method framework from the paper

[中文指南](method-framework.md)

Turn an understood and checked method into an editable, traceable diagram. Apply the same evidence standards as the reading: a plausible module is not necessarily one the paper implemented.

## 1. Extract relationships before arranging them

Read Methods, architecture figures and captions, input/output definitions, relevant equations, and training descriptions. First construct the following inventory internally; it can become the source table beneath the final diagram:

| Node/relationship | Inputs and outputs | Phase | Evidence location | Status |
| --- | --- | --- | --- | --- |
| Concrete module, data, loss, or edge | Meaning of objects or signals | Training/inference/evaluation | Page, section, figure, or equation | Explicit in source/inferred/unreported |

Include dimensions, parameters, rates, and equations only when verified. For work without a learned model, diagram the experiment, computation, or argument rather than inventing neural networks or losses.

Distinguish:

- Training data from deployment inputs, especially truth labels, future information, privileged states, and targets.
- Training objectives from model outputs, and loss calculation from parameter updates.
- Frozen from trainable modules, and offline training from online updates.
- Data flow from control feedback. Label feedback signals so a temporal loop is not mistaken for an instantaneous algebraic loop.
- The proposed method from baselines and ablation variants. Do not merge different experimental conditions into one system.

## 2. Choose content and scale

Start with one clear overview. Split a complex method into training and inference/deployment panels, with a detail panel if needed. About 5–12 main nodes per diagram is a useful starting point, not a hard limit. Prioritize inputs, central transformations, outputs, and important connections over a catalogue of hyperparameters.

Deliver: purpose and scope → diagram → caption and legend → source table for nodes and important edges → unreported, simplified, or unresolved details.

For control systems, check that nominal and residual actions are actually added, state feedback closes, and historical actions have clear time indices. Do not depict separate estimator and controller training as unsupported end-to-end joint optimization.

## 3. Default format: Mermaid

Use `flowchart LR` or `flowchart TB`, short `snake_case` IDs, and quoted labels. Use `subgraph` to separate phases where helpful. Include `accTitle` and a single-line `accDescr` for accessibility. Avoid theme-initialization directives, interactive links, scripts, and long labels.

Use solid edges for source-supported flow. If a necessary interpretation is inferred, use a dashed edge explicitly labeled “inferred.” Label parameter-update and feedback edges and explain them in the legend. Evidence status must not depend on color alone. Use limited `classDef` styling while keeping the figure interpretable in monochrome.

After close reading, diagram the actual method; a generic input–model–output placeholder is not a finished framework. See the [wind-aware RL example](../examples/wind-aware-rl-framework.en.md) for phase separation and source attribution.

## 4. Image and file export

Embed editable Mermaid source in chat or the report by default; rendering depends on the host. When saving is requested, provide Markdown with caption and source table, plus a standalone `.mmd` file if useful. If rendering is unavailable, provide source and disclose the limitation rather than claiming a rendered inspection.

For downloadable images, prefer SVG; use PNG for compatibility or an explicit resolution requirement. A local Mermaid renderer can render directly when available. Alternatively, reconstruct Graphviz `.dot` from the same checked relationships. If Graphviz is installed:

```bash
dot -Tsvg method-framework.dot -o method-framework.svg
dot -Tpng -Gdpi=300 method-framework.dot -o method-framework.png
```

Mermaid and Graphviz are different languages: do not feed `.mmd` directly to `dot`. If both sources are retained, check that nodes, edges, labels, and legends agree. Graphviz is an optional export dependency, not a requirement to install or invoke this skill. If a renderer is unavailable, keep editable source and respect the environment's installation permissions.

After export, check the file opens and is nonempty, labels are readable, nothing is clipped or obscured, and arrows point correctly. Use a temporary PNG preview for an SVG if needed. File existence alone does not establish correct layout; disclose when visual inspection was unavailable.

## 5. Delivery checks

- Does the diagram represent the paper, rather than only its abstract or the appearance of its original figure?
- Does ground truth appear only in the phases that actually permit it?
- Are feedback, residual addition, freezing, and training objectives represented correctly?
- Are consequential nodes and edges traceable, with inference labeled?
- Does the caption identify a reconstruction and explain the main simplifications?
- If only a diagram was requested, did the work focus on the necessary method checks rather than impose a full Pass 3 review?

This feature is an AI-reading extension of the project, not a claim that the source guide prescribed these drawing rules.
