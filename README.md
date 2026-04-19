# 风光产品贸易壁垒气候目标影响分析工具

**Wind & Solar Trade Policy Climate Impact Analysis Tool**

> 宫再佐 | 清华大学环境学院 | 指导教师：王灿教授  
> 硕士学位论文配套工具 · 2026年5月

---

## 工具简介 | Overview

本工具将**贸易均衡求解、贝叶斯双因素学习曲线拟合与气候目标对标分析**合并在同一框架下，实现从贸易政策冲击到气候目标缺口的定量计算。

工具覆盖光伏产业链3类产品（多晶硅、电池片、组件）与风电产业链2类产品（钢铁板材、风电机组），涵盖24个主要经济体及其余世界（ROW）共25个贸易主体，形成25×25双边贸易矩阵体系。

This tool integrates trade equilibrium modeling, Bayesian two-factor learning curve estimation (MCMC), and climate target benchmarking into a unified pipeline, enabling quantitative analysis from tariff scenarios to future installation gaps.

---

## 功能模块 | Modules

```
Pipeline Menu
├── 1. GSIM Simulation      — 贸易壁垒模拟：价格 / 产出 / 福利分析
├── 2. GSIM Visualisation   — 可视化：价格地图、贸易流量、福利图表
├── 3. Bayesian Learning    — MCMC 贝叶斯学习曲线估计
├── 4. Capacity Projections — 2023–2060 分国家装机量预测
├── 5. Carbon Alignment     — 巴黎协定目标对标与缺口分析
├── T. Tariff Scenarios     — 查看 / 编辑关税情景
├── E. Elasticities         — 查看 / 编辑弹性参数
└── M. Trade Matrices       — 查看内嵌 2023 年贸易矩阵
```

### 模块一：贸易模拟（GSIM）

基于 Armington（1969）差异化产品假设，采用局部均衡的**全球模拟模型（GSIM）**，输出：

| 输出内容 | 说明 |
|---------|------|
| 世界价格变化率（%） | 产品 × 国家 × 情景 |
| 双边贸易量变化矩阵 | 25×25 全矩阵 |
| 产出变化率（%） | 各国各产品 |
| 生产者剩余 ΔPS | 百万美元 |
| 消费者剩余 ΔCS | 百万美元 |
| 关税收入 ΔTR | 百万美元 |
| 净福利 ΔW | 百万美元 |

### 模块二：装机预测（贝叶斯学习曲线）

采用**双因素学习曲线模型**，将贸易冲击传导至装机成本（CAPEX）并预测未来装机轨迹：

```
log CAPEX_t = α + β·log(Q_t/Q_ref) + θ·log(P_t) + ε
```

- **β**：学习指数（反映累计装机带来的成本下降）  
- **θ**：投入价格弹性（反映供应链价格变化的传导系数）  
- **P_t**：由贸易模块输出的供应链综合投入价格指数

参数估计采用**Metropolis-Hastings MCMC**方法，以弱信息先验结合历史数据（2010–2023年）进行贝叶斯后验推断，并以Gelman-Rubin统计量（R̂ < 1.1）作为收敛判据。

### 模块三：气候目标对标（Carbon Alignment）

将装机预测路径与以下四类目标逐一比对，识别装机缺口、额外投资成本及目标延迟年份：

| 目标类型 | 说明 |
|---------|------|
| COP28 三倍目标 | 2030年可再生能源装机量三倍于2022年水平 |
| NDC 国家目标 | 各国国家自主贡献承诺下的风光装机路径 |
| IEA APS | 约束温升~2℃的公告政策情景路径 |
| IEA NZE | 约束温升1.5℃的净零排放路径 |

---

## 内置情景 | Built-in Scenarios

| 情景 | 标识 | 说明 |
|------|------|------|
| 基准情景 | S0 | 以2023年实际关税为基准，含MFN、FTA优惠税率及已生效惩罚性关税 |
| 升级情景 | S1 | 叠加2024年已实施或宣布的新增贸易壁垒措施 |
| 极端情景 | S2 | 最大保护主义假设下的反事实情景，代表壁垒影响的理论上界 |
| 自由贸易情景 | S3 | 以WTO环境商品协定（EGA）目标为参照的理想贸易情景 |

---

## 覆盖产品与国家 | Coverage

**产品（5类HS编码）**

| 产品 | HS编码 | 说明 |
|------|--------|------|
| 光伏多晶硅 | 280461 | 光伏电池核心原材料 |
| 光伏电池片 | 854142 | 将多晶硅加工为发电单元的中间产品 |
| 光伏组件 | 854143 | 最终安装于电站的成品 |
| 钢铁板材 | 720851 | 风电塔筒主要用钢 |
| 风电机组 | 850231 | 风力发电整机 |

**贸易主体（25个）**

澳大利亚、奥地利、比利时、巴西、日本、马来西亚、泰国、西班牙、荷兰、英国、美国、越南、土耳其、加拿大、中国、印度、法国、捷克、波兰、韩国、俄罗斯、德国、意大利、泰国、四班牙、世界其他（ROW）

> 25个经济体合计覆盖五类产品全球贸易额的95%以上。

---

## 环境配置 | Requirements

### Python 版本
Python ≥ 3.8

### 必要依赖
```bash
pip install numpy scipy matplotlib pandas openpyxl
```

### 可选依赖（世界地图可视化）
```bash
pip install geopandas shapely
```

> **说明**：若未安装 geopandas，价格地图、产出地图、贸易流量地图等将自动退为柱状图展示，不影响数值计算结果。首次运行时，工具会尝试自动下载 Natural Earth 地图数据（约2MB）并保存至 `data/` 目录；若本地已有 `ne_50m_admin_0_countries.shp`，则直接读取。

### 富文本界面（可选）
```bash
pip install rich
```

---

## 数据文件 | Data Files

仓库已包含以下数据文件，无需额外下载：

```
TradeSimulationTool/
├── wind_solar_pipeline_tool.py   # 主程序（单文件，内嵌2023年贸易矩阵与弹性参数）
├── data/
│   └── ELEC-C_irena.xlsx         # IRENA历史装机容量数据（2010–2023年）
├── irena_data/
│   ├── solar_pv_tic_by_country.csv      # 光伏分国别装机成本（TIC）
│   └── onshore_wind_tic_by_country.csv  # 风电分国别装机成本（TIC）
├── climate_targets.xlsx          # 各国气候目标数据（NDC/APS/NZE）
└── ne_50m_admin_0_countries.*    # Natural Earth 50m行政区划地图文件
```

> **内嵌数据**：2023年25×25双边贸易矩阵、各产品弹性参数（Armington替代弹性、进口需求弹性、出口供给弹性）及四类关税情景均已硬编码于主程序，无需外部CSV输入即可直接运行。

---

## 快速开始 | Quick Start

### 1. 克隆仓库

```bash
git clone https://github.com/ZaizuoGong/TradeSimulationTool.git
cd TradeSimulationTool
```

### 2. 安装依赖

```bash
pip install numpy scipy matplotlib pandas openpyxl geopandas shapely
```

### 3. 运行主程序

```bash
python wind_solar_pipeline_tool.py
```

启动后将进入交互式主菜单：

```
══════════════════════════════════════════════════════════════════════════
  Wind & Solar Trade Policy Pipeline Tool
  Output: D:\TradeSimulationTool\tool_outputs
──────────────────────────────────────────────────────────────────────────
PIPELINE MENU
  1  GSIM Simulation      — tariffs / welfare / price analysis
  2  GSIM Visualisation   — charts & maps from simulation
  3  Bayesian Learning    — MCMC estimation of learning curves
  4  Capacity Projections — 2023-2060 country projections
  5  Carbon Alignment     — Paris-target gap analysis
  ─  ─────────────────────────────────────────────────────
  T  Tariff Scenarios     — view / edit scenarios
  E  Elasticities         — view / edit Em, Ex, Es
  M  Trade Matrices       — view embedded 2023 data
  S  Save / Export        — JSON configs + CSV files
  P  Pipeline Status      — which steps are done
  0  Exit
```

---

## 典型使用流程 | Typical Workflow

以下按论文第4章示例分析的完整流程操作：

### Step 1：运行贸易模拟

```
主菜单 → 1（GSIM Simulation）→ 选择情景（如S1升级情景）→ Run
```

输出：世界价格变化、各国产出变化、25×25贸易流量矩阵、福利分解表。

### Step 2：生成可视化图表

```
主菜单 → 2（GSIM Visualisation）→ 选择图表类型
  7  Wind chain price maps   — 钢铁+风电机组价格地图
  8  PV chain price maps     — 多晶硅+电池片+组件价格地图
  9  5-product panel map     — 五产品综合面板地图
  A  Price scenarios maps    — 单产品跨情景比较
  6  Welfare choropleth maps — 福利变化世界地图
```

### Step 3：贝叶斯学习曲线估计

```
主菜单 → 3（Bayesian Learning）→ 1（加载IRENA数据）→ 2（运行MCMC估计）
```

- 默认运行4条MCMC链，每链20,000次有效采样（5,000次预烧）
- 自动输出后验分布图、学习曲线拟合图及参数汇总表
- 建议使用快速模式（Q键）进行测试（2,000采样 × 500预烧 × 1链）

### Step 4：装机量预测

```
主菜单 → 4（Capacity Projections）
```

读取Step 3的MCMC后验样本，结合贸易模拟输出的供应链价格指数，预测2023–2060年各国各情景下的风光累计装机轨迹。

> **注意**：根据稳健性检验，本工具对2050年前的预测结果具备较好的可信度（变异系数<20%），使用者应侧重于**情景间的相对比较**而非绝对数值，2050年后的长期预测存在发散风险，不建议直接用于政策决策。

### Step 5：气候目标对标

```
主菜单 → 5（Carbon Alignment）
```

输出：
- 各目标类型的装机缺口热力图（GW）
- 2030年/2050年截面地图
- 填补缺口所需的额外投资成本（亿美元）
- 贸易壁垒导致目标实现的延迟年份

---

## 参数调整 | Parameter Customization

工具的所有核心参数均可在运行时交互调整，无需修改源代码：

| 参数类型 | 入口 | 可调内容 |
|---------|------|---------|
| 关税情景 | 菜单 T | 各产品双边税率（MFN/FTA/惩罚性） |
| 弹性参数 | 菜单 E | Armington替代弹性σ、进口需求弹性η、出口供给弹性ε |
| MCMC超参数 | 贝叶斯菜单内 | 采样数、预烧步数、链数、随机种子 |
| 先验分布 | 源码 `_BAYES_PRIORS` | 学习指数β与投入价格弹性θ的先验均值与标准差 |
| 供应链权重 | 源码 `SUPPLY_CHAIN_WEIGHTS` | 各产品对CAPEX的成本占比权重 |

> **建议**：对于不同国别供应链结构、不同历史期弹性估计等情况，可参照论文第3章敏感性分析方案，通过调整弹性参数空间验证结论稳健性。

---

## 输出目录结构 | Output Structure

```
tool_outputs/
├── gsim_results/           # GSIM模拟结果（CSV + JSON）
├── bayesian_outputs/       # MCMC后验样本（mcmc_samples_{tech}.csv）
├── projection_data/        # 分国家装机量预测数据
├── figures/
│   ├── gsim/               # 价格地图、贸易流量图、福利图表
│   ├── bayesian/           # 后验分布图、学习曲线拟合图、CAPEX影响图
│   ├── projections/        # 装机轨迹图、目标缺口热力图
│   └── carbon_alignment/   # 巴黎协定对标图、延迟年份图
```

---

## 关键建模假设与局限性 | Limitations

使用本工具时请注意以下建模假设与局限性（详见论文第2.4节）：

1. **GSIM局部均衡假设**：模型聚焦单一市场，忽略跨产业要素流动与一般均衡反馈效应。对于在各国经济总量中占比较小的风光产品，该假设具有合理性。

2. **供应链传导假设**：各上游产品价格冲击按成本权重线性叠加传导至CAPEX，不考虑中间环节的吸收或放大效应，亦不区分国别供应链结构差异。

3. **投入价格弹性θ的识别**：历史数据期间（2010–2023年）缺乏投入价格的实质性变异，θ的后验分布主要由先验设定决定，对先验选择较为敏感。

4. **长期预测稳健性**：增长弹性γ对2050年后的装机量预测影响极大，存在严重发散风险。建议将2040年前的预测结果作为主要分析依据，并侧重情景间差异而非绝对数值。

5. **联合不确定性来源**：装机量情景间偏离的方差贡献超过90%来自贸易弹性参数，表明可信的贸易弹性设定是保证结论有效性的关键。

---

## 引用 | Citation

如在研究中使用本工具，请引用：

```
宫再佐. 风光产品贸易壁垒对气候目标实现的影响分析工具构建研究
[D]. 北京：清华大学，2026.
```

```
Gong Zaizuo. A Tool for Analyzing the Impact of Wind and Solar Trade 
Barriers on Climate Target Attainment [D]. Beijing: Tsinghua University, 2026.
```

---

## 许可 | License

本工具代码与数据均设为开源，供学术研究与政策分析使用。

---

## 联系 | Contact

**作者**：宫再佐  
**单位**：清华大学环境学院  
**指导教师**：王灿 教授  
**邮箱**：gongsaisou01@gmail.com  
**GitHub**：[ZaizuoGong/TradeSimulationTool](https://github.com/ZaizuoGong/TradeSimulationTool)
