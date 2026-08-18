<div align="center">
  <h1>agent-plugin-prd</h1>
  <p>PRD 工作流技能包 — 从自然语言需求到可交付 PRD 的完整工具链</p>

  <div>
    <span>📦 版本: v1.1.0</span> ·
    <span>📄 协议: MIT</span> ·
    <span>🔧 平台: Claude Code 插件</span>
  </div>

  [技能概览](#技能概览) • [快速开始](#快速开始) • [Agents](#agents) • [典型工作流](#典型工作流) • [使用技巧](#使用技巧)
</div>

---

## 技能概览

本插件提供六个技能 + 三个 Agent，覆盖 PRD 从生成到技术预研的完整生命周期：

| 技能 | 作用 | 触发方式 |
|------|------|----------|
| **product-knowledge-sync** | 按产品类型同步知识库，为后续技能提供产品上下文 | `/product-knowledge-sync` 或由其他技能自动调用 |
| **spec-drafter** | 将自然语言需求转为结构化 PRD | `/spec-drafter <需求描述>` |
| **spec-clarifier** | 对 PRD 进行 10 维度歧义检测与逐轮澄清 | `/spec-clarifier <PRD路径>` |
| **spec-checker** | 从结构、完整性、准确性三个维度对 PRD 严苛评分 | `/spec-checker <PRD路径>` |
| **spec-analysis** | 对已澄清的 PRD 进行技术可行性分析，含合理性检查、依赖分析、架构影响评估、NFR 推导，分析完成后自动衔接技术预研 | `/spec-analysis <PRD路径>` |
| **tech-research** | 对 PRD 中的技术不确定性进行预研验证，输出预研报告和 PRD 修改建议 | `/tech-research <PRD路径>` |

## Agents

本插件提供三个专用 Agent：

| Agent | 职责 | 关键能力 | 触发方式 |
|-------|------|----------|----------|
| **product-expert** | 分析需求与现有产品功能的关系 | 功能归属判断、决策冲突检测、假设提取、知识边界标注 | spec-analysis 自动调用 |
| **system-architect** | 分析需求的技术依赖和架构影响 | 依赖识别、差距判断、数据流影响、侵入评估、风险识别 | spec-analysis 自动调用 |
| **action-researcher** | 对可行性报告的"技术预研""风险缓解"类行动项执行技术预研以消除风险和不确定性 | 行动项筛选、并行分发研究、输出预研报告、PRD 修改建议 | spec-analysis Phase 5 自动触发或 `/action-researcher <报告路径>` |

## 快速开始

## 构建与发布

本仓库是 PRD 插件的 source of truth。Claude Code、DSH 和 npm 侧消费的内容都由构建脚本生成，不直接把源码目录当作发布产物使用。

### 构建产物

```bash
# 生成全部产物
npm run build

# 仅生成 Claude marketplace 产物
npm run build:claude

# 仅生成 DSH bundle 产物
npm run build:dsh

# 仅生成 npm 包目录
npm run build:npm
```

输出目录：

| 目录 | 用途 |
|------|------|
| `dist/claude` | Claude Code marketplace 可发布产物 |
| `dist/dsh` | DSH skill / agent bundle 产物 |
| `dist/npm` | npm 发布目录 |

每个产物目录都会生成 `build-meta.json`，记录版本、source commit、目标平台和 artifact hash。

### 同步到 marketplace 仓库

```bash
npm run build:claude
npm run publish:marketplace:local
```

默认同步到相邻仓库：

```text
../agent-plugin-marketplace/artifacts/agent-plugin-prd
```

如需指定其他 marketplace 仓库路径：

```bash
MARKETPLACE_REPO=/path/to/agent-plugin-marketplace npm run publish:marketplace:local
```

### 安装

```bash
# Step 1 — 添加插件市场
claude plugin marketplace add https://github.com/aic-bg/agent-scaffold-marketplace.git

# Step 2 — 安装插件
claude plugin install agent-plugin-prd-dev@agent-scaffold-marketplace
```

### 基础用法

```bash
# Step 1 — 生成 PRD
/spec-drafter 我需要一个模型路由功能，能根据请求内容自动选择最合适的模型

# Step 2 — 澄清歧义
/spec-clarifier docs/specs/spec-model-routing.md

# Step 3 — 评审质量
/spec-checker docs/specs/spec-model-routing.md

# Step 4 — 可行性分析（分析完成后自动衔接技术预研）
/spec-analysis docs/specs/spec-model-routing.md

# Step 5 — 技术预研（也可由 spec-analysis 自动触发）
/tech-research docs/specs/spec-model-routing.md
```

## 典型工作流

```text
product-knowledge-sync → spec-drafter → spec-clarifier → spec-checker → spec-analysis → action-researcher / tech-research
    (加载知识库)          (生成PRD)      (消除歧义)       (质量门禁)      (可行性分析)    (预研衔接 / 技术预研)
```

完整流程为 **drafter → clarifier → checker → analysis**，analysis 完成后自动衔接技术预研。

### 阶段一：PRD 生成与澄清

**1. product-knowledge-sync** — 同步产品知识库

首次使用时交互式选择产品类型并配置知识库来源。ACG 产品类型内置默认知识库地址，其他类型支持 git clone 或本地目录加载。配置持久化到 `.agent-plugin-prd.json`，后续运行自动跳过询问。

**2. spec-drafter** — 生成 PRD

基于 `references/spec-template.md` 模板，将需求描述转化为包含以下章节的结构化文档：

- 概述与问题陈述
- 核心价值目标
- 用户场景（含优先级、验收标准、Given/When/Then 场景）
- 功能需求（含可测试的验收标准）
- 成功标准（可度量、技术无关）
- 假设与约束

输出路径：`docs/specs/spec-<功能名>.md`

**3. spec-clarifier** — 澄清 PRD 歧义

委托子Agent执行10维度（功能范围、数据模型、交互流程、非功能质量、集成依赖、边界情况、约束权衡、术语一致性、完成信号、占位符）结构化扫描，主Agent接收歧义清单后逐轮交互，每轮最多提出 5 个关键问题，答案即时编码回文档。支持多轮运行直至歧义消除。

**4. spec-checker** — 评审 PRD 质量

并行委托三个子Agent分别评审结构规范（10%）、内容完整性（20%）、内容准确性（70%），主Agent汇总加权评分，输出结构化评审报告：

| 评级 | 分数 |
|------|------|
| 优秀 | ≥ 9.0 |
| 良好 | ≥ 7.5 |
| 需改进 | ≥ 6.0 |
| 不合格 | < 6.0 |

输出路径：`docs/reviews/review-<功能名>-<日期>.md`

### 阶段二：技术可行性分析

**5. spec-analysis** — 可行性分析

编排 product-expert 和 system-architect 两个 Agent 并行分析，产出可行性报告：

- 产品合理性检查（功能归属、决策冲突检测）
- 未确认假设清单（5类：部署约束/技术绑定/存储承诺/兼容性/外部依赖）
- 技术依赖分析（依赖项识别、当前架构满足度、差距说明）
- 数据流影响（Mermaid flowchart）+ 侵入程度评估
- 关键风险与约束（Top 3-5，含缓解方向）
- 技术复杂度评估（不可能三角 + 多维度评级）
- 隐含 NFR 推导
- 可行性结论 + 推荐交付路径

> [!TIP]
> 报告中**不建议保留"下一步行动"表**。可行性分析的结论应直接通过 Phase 5 自动衔接技术预研（tech-research / action-researcher），行动项由预研流程统一管理和追踪，避免报告中出现待处理僵尸条目。

分析完成后自动衔接预研：提取"技术预研""风险缓解"类行动项 → 用户选择触发方式（全部触发 / 选择触发 / 稍后手动） → 委托 action-researcher 执行预研 → 输出预研报告 + PRD 修改建议。

**6. tech-research** — 技术预研

识别 PRD 中的技术不确定性，制定预研计划，并行分发研究 Agent，输出预研报告和 PRD 修改建议。既可由 spec-analysis 自动触发，也可从 PRD 直接手动触发。

**7. action-researcher** — 预研衔接（由 spec-analysis Phase 5 自动触发或手动 `/action-researcher`）

承接可行性分析的行动项，关联风险/假设上下文，并行分发研究子Agent，输出预研报告 + 状态更新清单 + PRD 修改建议。形成 可行性分析 → 预研 → 回填 → PRD更新 的完整闭环。

## 使用技巧

- **需求描述越具体，PRD 质量越高** — 尽量说明角色、场景、操作和约束
- **spec-clarifier 可重复执行** — 未覆盖的高影响维
- **产品规划只需两步** — 产品经理进行产品规划时，仅需 `spec-drafter` 和 `spec-clarifier` 即可完成高质量 PRD 输出，`spec-checker` 之后为技术侧流程，可根据需要选择性执行