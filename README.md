# Crescent
A Unified Framework for Multimodal Recommendation under Modality Missingness

<div align="center">

<img src="./assets/crescent_banner.svg" alt="Crescent Banner" width="100%"/>

<br/>

<p>
  <a href="#"><img src="https://img.shields.io/badge/license-MIT-blue.svg" alt="License"/></a>
  <a href="#"><img src="https://img.shields.io/badge/python-3.9+-blue.svg" alt="Python"/></a>
  <a href="#"><img src="https://img.shields.io/badge/PyTorch-1.13+-ee4c2c.svg" alt="PyTorch"/></a>
  <a href="#"><img src="https://img.shields.io/badge/built_on-MMRec-c89b4a.svg" alt="MMRec"/></a>
  <a href="#"><img src="https://img.shields.io/badge/status-WIP-yellow.svg" alt="Status"/></a>
</p>

<p><i>人有悲欢离合，月有阴晴圆缺 —— 苏轼《水调歌头》</i></p>

</div>

---

## 关于项目名 · About the Name

**Crescent（新月）** 取意苏轼《水调歌头》"月有阴晴圆缺"。新月在视觉上是一弯被照亮的月牙，背后是被遮蔽的完整球体——我们看到的不完整，从来不意味着本体不完整。多模态推荐中的 item 也是如此：很少有 item 同时拥有完整的图像、文本、音频等所有模态。冷启动商品先有标题没有图，UGC 视频先有画面没有描述，长尾 item 的模态质量系统性更差。我们观察到的，往往只是 item 的"一弯新月"。Crescent 这个词源自拉丁语 *crescere*（"生长"）。新月不是缺陷的终态，而是生长过程的起点——它会自然变成满月。这正是本框架的哲学立场：**不把模态缺失看作待修补的瑕疵，而看作待生长的潜在表征**。

| Crescent 意象 | 多模态推荐对应 |
|---|---|
| 月亮被照亮的一弯 | item 的可见模态 |
| 月亮被遮蔽的部分 | item 的缺失模态 |
| 完整的月球本体 | item 的完整多模态表征 |
| 新月生长为满月 | 通过生成模型补全缺失模态 |
| 我们对"月亮是球"的先验 | 协同信号、类目结构等推荐场景先验 |

---

## Crescent · Overview

**Crescent** 是一个专注于**多模态缺失场景**下的多模态推荐系统框架。
当前多模态推荐研究中，"模态缺失"的研究面临如下问题：
- 📉 **基准不统一**：各论文模态缺失的设置五花八门，缺失比例、粒度、模式互不可比
- 🎭 **设定不真实**：现有评估几乎都是 MCAR（完全随机缺失），但真实场景下缺失是**与流行度、冷启动、类目高度耦合的非随机过程**
- 🔧 **方法范式割裂**：扩散生成式、不变学习、补全式等方法各有 setting，缺乏统一比较平台

Crescent 试图回应这三个问题：
1. 🌒 **统一基准**：定义覆盖 MCAR / MAR / MNAR 以及推荐场景特有缺失模式的评估协议
2. 🌓 **方法库**：复现并统一接入当前主流的多模态缺失推荐方法（DiffMM, MILK, DGMRec 等）
3. 🌔 **新方法**：提出基于条件生成的多模态缺失推荐新算法（开发中）

---

## 核心特性 · Key Features

### 🌑 统一的模态缺失基准 (Benchmark)

定义五类缺失场景，覆盖学术理想到工业实际：

- **MCAR**: 完全随机缺失（学术标准设定）
- **Popularity-biased Missing**: 长尾 item 模态质量更差
- **Cold-start-induced Missing**: 新 item 先有某些模态后有其他
- **Category-skewed Missing**: 不同类目模态可用性系统性不同
- **Quality-degraded Missing**: 模态存在但质量低（噪声/低分辨率）

### 🌒 统一的方法接口

定义 `ModalityMissingHandler` 基类，把"模态缺失处理"抽象成可插拔阶段。所有方法——无论是扩散生成式、不变学习式还是补全式——都遵循统一的输入输出契约，但内部实现完全自由。

### 🌓 复现并集成 SOTA 方法

| 方法 | 范式 | 出处 | 状态 |
|---|---|---|---|
| DiffMM | 扩散生成式 | KDD'24 | 🔧 In Progress |
| MILK | 不变学习 | ACM MM'24 | 🔧 In Progress |
| DGMRec | 解耦图生成 | WWW'25 | 🔧 In Progress |
| MENTOR | 层次化多模态 | AAAI'24 | 📋 Planned |
| Mirror Gradient | 扁平最小化 | WWW'24 | 📋 Planned |
| MGCN | 多视图图卷积 | MM'23 | 📋 Planned |

### 🌔 公平的评估协议

- 统一数据集 split 与随机种子
- 显式报告**推理开销**（扩散方法的多步采样代价不藏起来）
- 多种缺失场景下的鲁棒性曲线

### 🌕 新方法：CrescentGen（开发中）

基于条件生成的多模态缺失推荐方法：
- 生成目标不是"重建真实模态"，而是"生成对下游推荐最有用的伪模态表征"
- 协同信号、流行度、类目结构作为生成的条件
- 详细设计文档参见 [`docs/method.md`](docs/method.md)

---

## 快速开始 · Quick Start

### 环境要求

```
python >= 3.9
pytorch >= 1.13
```

### 安装

```bash
git clone https://github.com/<your-org>/Crescent.git
cd Crescent
pip install -r requirements.txt
```

### 运行一个 Baseline

```bash
# 在 Amazon-Baby 数据集上运行 DiffMM，使用 popularity-biased 缺失场景
python main.py \
  --model DiffMM \
  --dataset baby \
  --missing_mode popularity_biased \
  --missing_ratio 0.5
```

### 在自己的方法上集成 Crescent 评估

```python
from crescent.handler import ModalityMissingHandler
from crescent.benchmark import MissingScenario, evaluate

class YourMethod(ModalityMissingHandler):
    def forward(self, modalities, masks, **kwargs):
        # 你的实现
        return processed_modalities, aux_losses

# 在统一的缺失场景下评估
results = evaluate(
    method=YourMethod(),
    dataset='baby',
    scenarios=[
        MissingScenario.MCAR(ratio=0.3),
        MissingScenario.PopularityBiased(ratio=0.5),
        MissingScenario.ColdStart(),
    ]
)
```

---

## 项目结构 · Project Structure

```
Crescent/
├── crescent/
│   ├── data/              # 数据加载 + 缺失模式注入
│   │   ├── loader.py
│   │   └── missing_simulator.py
│   ├── handler/           # ModalityMissingHandler 基类
│   │   └── base.py
│   ├── methods/           # 各方法实现
│   │   ├── diffmm/
│   │   ├── milk/
│   │   ├── dgmrec/
│   │   └── crescent_gen/  # 我们提出的新方法
│   ├── backbone/          # 推荐 backbone (LightGCN, FREEDOM, ...)
│   ├── benchmark/         # 评估协议与场景定义
│   └── trainer/           # 训练循环
├── configs/               # 每个方法+数据集组合的 YAML 配置
├── docs/                  # 设计文档
├── scripts/               # 复现实验脚本
└── README.md
```

---

## 缺失场景示意 · Missing Scenarios

```
完整模态                  MCAR                Popularity-biased        Cold-start
(满月)                   (均匀缺月)            (长尾月更缺)             (新月最缺)

  Item A                   Item A               Item A (popular)        Item A (new)
   🌕 V T A                  🌖 V _ A             🌕 V T A                🌗 V _ _
  Item B                   Item B               Item B (popular)        Item B (old)
   🌕 V T A                  🌗 V T _             🌕 V T A                🌕 V T A
  Item C                   Item C               Item C (long-tail)      Item C (new)
   🌕 V T A                  🌖 _ T A             🌒 V _ _                🌗 _ T _
```

V=Visual, T=Text, A=Audio  ·  🌕=完整  🌖🌗=部分缺失  🌒=严重缺失

---

## 引用 · Citation

如果 Crescent 对你的研究有帮助，请引用：

```bibtex
@misc{crescent2026,
  title  = {Crescent: A Unified Framework for Multimodal Recommendation under Modality Missingness},
  author = {orangeai},
  year   = {2025},
  url    = {https://github.com/<your-org>/Crescent}
}
```


```bibtex
@inproceedings{zhou2023mmrec,
  title     = {MMRec: Simplifying Multimodal Recommendation},
  author    = {Zhou, Xin},
  booktitle = {ACM Multimedia Asia Workshops},
  year      = {2023}
}
```

---

## 许可证 · License

本项目采用 MIT License。详见 [LICENSE](LICENSE)。

---

## 致谢 · Acknowledgement

- 本项目构建于 [MMRec](https://github.com/enoche/MMRec) 之上，感谢 Xin Zhou 的工作
- 项目名 *Crescent* 取意苏轼《水调歌头》"月有阴晴圆缺"，纪念那位在缺憾中看到诗意的诗人

<div align="center">

<br/>

🌒 → 🌓 → 🌔 → 🌕

*from a crescent to a full moon, from a glimpse to the whole.*

</div>
