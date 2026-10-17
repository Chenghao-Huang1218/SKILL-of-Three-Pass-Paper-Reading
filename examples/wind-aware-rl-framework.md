# 论文方法框架图：风感知残差控制

[English version](wind-aware-rl-framework.en.md) · [完整精读](wind-aware-rl-reading.md)

依据 Tasim 与 Sun 的 [arXiv:2607.01528v1](https://arxiv.org/abs/2607.01528v1) 重建，展示方法框架图功能。下图是本项目的解释性重建，不是论文原图，也没有通过代码或实验复现验证。页码指已阅读的30页v1 PDF。

## 1. 训练流程概览

```mermaid
flowchart LR
    accTitle: Wind-Aware Training Pipeline Overview
    accDescr: 先用PD飞行数据和平均风标签训练估计器，再冻结估计器，用仿真交互训练PPO残差策略；不是联合端到端训练。
    flight_data["PD飞行数据<br/>机载信号与平均风标签"]
    estimator_train["监督训练GRU与注意力<br/>Huber及速度、方向辅助项"]
    frozen_estimator["冻结风估计器"]
    rollouts["仿真交互<br/>风况与轨迹采样"]
    policy_train["PPO残差策略训练<br/>跟踪、控制量与变化率奖励"]
    saved_policy["保存策略用于评估"]
    flight_data --> estimator_train
    estimator_train --> frozen_estimator
    frozen_estimator -->|"在线提供估计风"| policy_train
    rollouts --> policy_train
    policy_train --> saved_policy
```

训练目标只在训练图中出现：估计器采用分量回归损失及速度、方向辅助项；PPO依据跟踪误差、残差幅度和变化率等奖励训练。标签是仿真平均风；部署图不把它接入策略。【pp.7–10、16–17，式2】本概览把RL交互与更新合并为一个阶段，没有逐步展开PPO优化。

## 2. 定高水平飞行的部署闭环

```mermaid
flowchart LR
    accTitle: Wind-Aware Deployment Control Framework
    accDescr: 水平模型从机载与指令特征估计平均风，滤波后供PPO使用；PD名义控制和滤波残差相加后作用于飞行器，反馈来自机载状态。
    reference["参考轨迹"]
    state_signals["机载状态反馈"]
    command_features["控制指令特征<br/>闭环来源待核查"]
    features["水平模型特征与历史<br/>48维；60点窗口"]
    estimator["冻结的Attention-GRU<br/>估计北向、东向平均风"]
    ema_filter["EMA与尖峰门控<br/>时间常数3秒"]
    pd_controller["名义PD控制器"]
    residual_policy["PPO残差策略<br/>23维观测"]
    action_filter["残差缩放、平滑<br/>与变化率限制"]
    action_sum["名义控制量＋残差"]
    vehicle["四旋翼动力学<br/>含执行器响应"]
    ambient_wind["环境风与湍流<br/>不是在线风真值输入"]
    reference --> pd_controller
    state_signals --> pd_controller
    state_signals --> features
    command_features --> features
    features --> estimator
    estimator --> ema_filter
    ema_filter -->|"两个估计风分量"| residual_policy
    state_signals --> residual_policy
    reference -->|"计算跟踪误差"| residual_policy
    pd_controller -->|"归一化基线推力"| residual_policy
    pd_controller -->|"名义推力与力矩"| action_sum
    residual_policy -->|"推力与三轴力矩修正"| action_filter
    action_filter --> action_sum
    action_filter -->|"上一时刻已施加残差"| residual_policy
    action_sum --> vehicle
    ambient_wind --> vehicle
    vehicle --> state_signals
```

实线表示原文支持的数据或控制关系；带“上一时刻”的回路表示历史动作，不是瞬时代数环。本图聚焦水平估计器；升降估计器采用22维输入，未与48维水平方案混成一个网络。【pp.9–12、16】

**SVG预览：**

![依据论文重建的定高水平飞行部署框架，PD名义量与PPO残差在作用于飞行器前相加，真实风不输入在线策略](wind-aware-rl-framework.svg)

[打开SVG](wind-aware-rl-framework.svg) · [Graphviz源文件](wind-aware-rl-framework.dot)

## 3. 模块与关键连接的原文依据

| 图中内容 | 来源 | 解读边界 |
| --- | --- | --- |
| PD生成监督数据；传感器和控制指令特征 | pp.5–7、9–10 | 部署时指令特征取自基线还是最终修正指令，未充分明确；图中单列输入并标注待核查 |
| 平均风标签及监督损失 | pp.7–9 | 标签不含瞬时湍流；只出现在训练流程 |
| 48维特征、60点历史窗口、冻结GRU | pp.8–10、16 | 历史均值本身可依赖更长过去，不能把6秒窗口当作全部记忆 |
| EMA及尖峰门控进入策略 | pp.14、16 | 3秒为时间常数，不表示固定3秒纯延迟 |
| 策略接收状态、风、误差、基线推力及历史残差 | p.16 | 图中合并状态分量，省略观测归一化细节 |
| 策略输出、缩放、平滑及变化率限制 | pp.16–17 | 各级限幅与电机混控的精确顺序需代码核查 |
| PD直接分支与残差相加 | pp.15–17；结合p.8，Fig.1 | 依据正文补清名义控制的直接路径，不声称逐线复制原图 |
| 环境扰动、动力学与状态反馈 | pp.5–7、16 | 反馈为简化总线，不表示无噪声真值状态；没有逐项绘制传感器采样与延迟 |
| 冻结估计器后训练PPO，奖励来自跟踪与控制项 | pp.16–17，式2 | 不存在本文证据支持的联合端到端训练箭头 |

## 4. 本图刻意保留的边界

- 没有把仿真风真值、全飞行平均值或未来状态接入在线策略。
- 没有把风输入置零的消融画成一项独立重训方法。
- 没有声称实际读取频率、缓冲更新、指令特征来源或约束执行顺序已通过实现核查。
- 图只解释结构，不证明性能、因果机制或稳定性；完整判断见[精读示例](wind-aware-rl-reading.md)。

读者可复制本页的Mermaid修改；SVG由同一部署节点与连线关系重建的Graphviz源文件导出。训练概览保留为Mermaid，不包含在部署SVG中。
