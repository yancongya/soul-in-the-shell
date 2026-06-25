# Soul in the Shell — 设计语言框架

通用的设计语言框架，为项目定义有温度的文案和交互风格。每个项目通过自身的 `soul.json` 和 `shell.json` 配置具体的声音人格和视觉外壳，本框架提供方法论和结构规范。

> 灵魂住在壳里，壳保护灵魂，灵魂赋予壳意义。

---

## 架构

```
skills/soul-in-the-shell/SKILL.md  ← 本文件，通用框架（稳定）
项目根目录/soul.json            ← 灵魂：文案、语气、人格（说什么）
项目根目录/shell.json           ← 外壳：UI 组件、视觉风格、Token（怎么穿）
```

- **SKILL.md** 定义「怎么定义」— 方法论、结构、约束
- **soul.json** 定义「说什么」— 人格、语气、文案规则
- **shell.json** 定义「怎么穿」— 组件样式、视觉语言、设计 Token

三者独立演进：框架可以增加新的维度，项目可以随时调整自己的灵魂和外壳。

### 为什么拆分 soul 和 shell

- **灵魂会变**：产品调性可能从「专业严谨」调整为「轻松幽默」，只改 soul
- **外壳会变**：设计系统从拟态换成极简，只改 shell
- **独立迭代**：文案团队改 soul，设计团队改 shell，互不干扰
- **复用可能**：多个项目可以共享同一个 shell（视觉一致），但有不同 soul（人格不同）

### soul 如何指导 shell

shell 不是独立存在的，它的风格由 soul 推导而来。shell.json 中的 `styleDerivation` 字段记录这个推导过程。

```
soul.persona.keywords → shell.tokens.color 倾向
  「幽默」→ 暖色、有活力的 accent
  「专业」→ 低饱和、冷静色调
  「温暖」→ 柔和阴影、大圆角

soul.voice.is → shell.tokens.typography + spacing
  「轻松」→ 字号适中、行高宽松
  「严谨」→ 字号精确、间距紧凑
  「有活力」→ 动效频率高、时长短

soul.voice.isNot → shell.antiPatterns
  「不油腻」→ 避免过度渐变、花哨装饰
  「不冷冰冰」→ 避免纯黑白、硬边角

soul.toneByScene → shell.components 各状态的色彩和动效
  「错误时温和」→ error 色用柔和值，动效用 ease-out
  「成功时轻快」→ success 色明亮，动效稍快
```

**推导示例：**

```json
// soul.json
{
  "persona": { "keywords": ["幽默", "耐心", "有料"] },
  "voice": {
    "is": "轻松幽默、有干货、不端着",
    "isNot": "油腻卖萌、居高临下、冷冰冰"
  }
}

// → 推导出 shell.json
{
  "style": "温暖拟态",
  "styleDerivation": "从 soul「幽默诙谐的设计老师」推导：温暖的 primary 色 + 柔和圆角 + 轻量动效 = 老师的亲和力，不端着但专业",
  "tokens": {
    "color": { "primary": "oklch(0.62 0.165 50)" },  // 暖色
    "radius": { "lg": "10px" },                       // 柔和圆角
    "animation": { "duration": { "slow": "300ms" } }  // 不急不躁
  }
}
```

---

## soul.json 结构

```json
{
  "project": "项目名称",
  "tagline": "一句话定位",
  "persona": {
    "who": "这个产品的「人格」是谁",
    "keywords": ["关键词1", "关键词2", "关键词3"],
    "description": "用一段话描述这个产品和用户对话时的感觉"
  },
  "voice": {
    "person": "你",
    "aiPerson": "我",
    "is": "品牌声音是什么（3-5 个特征）",
    "isNot": "品牌声音不是什么（边界定义）",
    "style": "口语化、简短、有温度",
    "punctuation": {
      "period": false,
      "ellipsis": false,
      "tilde": false
    },
    "toneByScene": {
      "success": "成功时语气偏什么",
      "error": "错误时语气偏什么",
      "empty": "空状态时语气偏什么",
      "guide": "引导时语气偏什么"
    }
  },
  "copy": {
    "success": [],
    "error": [],
    "empty": [],
    "confirm": [],
    "guide": [],
    "button": []
  },
  "antiPatterns": [],
  "referenceSentences": [],
  "audit": {
    "dimensions": ["调性一致", "用词规范", "句式简洁", "情感恰当"],
    "scoring": {
      "pillarAdherence": 30,
      "lexicalCompliance": 25,
      "sentenceClarity": 20,
      "toneConsistency": 15,
      "emotionalTemperature": 10
    },
    "passThreshold": 80
  }
}
```

---

## shell.json 结构

```json
{
  "project": "项目名称",
  "style": "视觉风格名称",
  "principles": [],
  "tokens": {
    "color": {},
    "spacing": {},
    "radius": {},
    "shadow": {},
    "typography": {},
    "animation": {}
  },
  "components": {},
  "layout": {},
  "density": {},
  "antiPatterns": []
}
```

---

## 框架维度

### soul.json 维度

#### 1. 人格定义（persona）

回答三个问题：
- **这个产品是谁？** — 不是「AI 助手」，可以是「幽默老师」「靠谱搭档」「话少的专家」
- **Ta 的三个关键词？** — 决定所有文案的底色
- **Ta 怎么说话？** — 一段话描述对话感觉

#### 2. 语气规范（voice）

- **人称**：统一用什么称呼用户
- **is / isNot**：品牌声音「是什么」和「不是什么」，划定边界
- **语气光谱（toneByScene）**：voice 固定，tone 按场景调节
  - voice 是品牌的 DNA，不变
  - tone 是情绪的音量旋钮，随场景调大调小
- **标点**：哪些标点禁用、哪些慎用
- **句式**：短句为主？允许长句？

#### 3. 文案规则（copy）

按场景分类，每个场景提供：
- ❌ 反面示例（不要这样写）
- ✅ 正面示例（这样写）

场景至少覆盖：成功、错误、空状态、确认、引导、按钮

#### 4. 反模式（antiPatterns）

明确列出禁止的表达，比正面示例更重要。

#### 5. 参考语调（referenceSentences）

5-10 句代表目标调性的句子，读一遍就能感受风格。

#### 6. 内容审计（audit）

定义文案写好后的验证机制：
- **dimensions**：审计维度（调性一致、用词规范、句式简洁、情感恰当）
- **scoring**：各维度权重（总和 100）
- **passThreshold**：通过阈值（0-100）

Agent 产出文案后，可按此机制自检是否符合 soul.json 的风格要求。

### shell.json 维度

#### 1. 设计原则（principles）

2-4 条核心设计信念，决策冲突时的仲裁依据。

#### 2. 设计 Token（tokens）

将视觉规范参数化，从描述变为可执行：

```json
{
  "tokens": {
    "color": {
      "primary": "hsl(var(--primary))",
      "success": "hsl(154 80% 60%)",
      "error": "hsl(346 80% 62%)"
    },
    "spacing": {
      "xs": "4px",
      "sm": "8px",
      "md": "16px",
      "lg": "24px",
      "xl": "32px"
    },
    "radius": {
      "sm": "6px",
      "md": "8px",
      "lg": "12px",
      "xl": "16px"
    },
    "shadow": {
      "sm": "0 1px 2px rgba(0,0,0,0.05)",
      "md": "0 4px 6px rgba(0,0,0,0.07)",
      "lg": "0 10px 15px rgba(0,0,0,0.1)",
      "xl": "0 20px 25px rgba(0,0,0,0.15)"
    },
    "typography": {
      "fontFamily": { "sans": "Inter, system-ui", "mono": "Fira Code, monospace" },
      "scale": 1.25,
      "lineHeight": { "tight": 1.25, "normal": 1.5, "relaxed": 1.75 }
    },
    "animation": {
      "duration": { "fast": "150ms", "normal": "200ms", "slow": "300ms" },
      "easing": { "enter": "ease-out", "exit": "ease-in", "standard": "ease-in-out" }
    }
  }
}
```

#### 3. 组件规范（components）

每个组件包含：
- **style**：视觉风格描述
- **features**：必须有的视觉特征
- **forbid**：禁止的做法
- **pattern**：常见变体的结构模板

至少覆盖：modal、toast、emptyState、button、tooltip、icon、guide

#### 4. 布局（layout）

工具栏、侧栏、导航栏的位置和尺寸。

#### 5. 密度（density）

不同信息密度的切换模式。

#### 6. 动效（animation）

动效原则、时长规范、禁止的动效类型。

#### 7. 反模式（antiPatterns）

UI 层面的反模式，与 soul.json 的文案反模式互补：
```
| 反模式 | 修复 |
|--------|------|
| 全居中布局 | 加不对齐或左对齐 |
| 无动画 | 加有意义的动效 |
| 纯色背景 | 加纹理或渐变 |
| 默认阴影 | 用分层的氛围感阴影 |
```

---

## 使用方式

### 新项目接入

1. 复制 `soul.json` 和 `shell.json` 模板到项目根目录
2. 根据项目调性填写各字段
3. 开发时参考 soul.json（文案）和 shell.json（UI）
4. 遇到新场景，补充到对应文件

### 迭代更新

- soul.json 和 shell.json 可随时修改，不影响框架
- 框架增加新维度时，项目 json 自动兼容（可选字段）
- 定期回顾 referenceSentences 和 components，确保风格一致

### Agent 工作流

**改文案时：**
1. 读 soul.json → 理解 persona + voice.is/isNot
2. 按 12 个维度全面扫描 → 不只看 toast 和空状态
3. 按 copy 规则写 → 遵循 antiPatterns
4. 用 audit 机制自检 → 确保通过阈值
5. 新场景补充回 soul.json

**改 UI 时：**
1. 读 shell.json → 理解 principles + tokens
2. 按 components 规范写 → 使用 tokens 而非硬编码值
3. 遵循 animation 原则和 antiPatterns
4. 新组件规范补充回 shell.json

**从已有项目提取时：**
1. 扫描项目结构 → 定位文案文件和样式文件
2. 按 12 个维度收集文案样本（toast/空状态/按钮/错误/引导/确认弹窗/标题标签/描述文案/placeholder/警告框/状态标签/表单标签）
3. 分析人称、语气、情绪、幽默感、专业度
4. 收集样式样本（CSS 变量/Tailwind 配置/组件结构）
5. 生成 soul.json 和 shell.json 初稿
6. 用 audit 机制验证现有文案是否通过阈值
7. 补充 antiPatterns

---

## 从已有项目提取

当面对一个已有项目但没有 soul.json / shell.json 时，可以通过分析现有代码和文案反向提取。

### 提取 soul.json（文案灵魂）

**扫描目标（12 个维度，缺一不可）：**

| # | 维度 | 搜索关键词 | 常见违规 |
|---|------|-----------|----------|
| 1 | Toast / 提示 | `showToast`、`toast(`、`message.success` | 「已保存」「操作成功」 |
| 2 | 空状态 | `暂无`、`没有`、`还没有`、`empty` | 「暂无XX」冷冰冰 |
| 3 | 按钮文案 | `<Button`、`<button` 内的文字 | 「确认」「取消」太正式 |
| 4 | 错误信息 | `catch`、`error`、`错误`、`失败` | 「请求失败」「错误代码」 |
| 5 | 引导弹窗 | `guide`、`tutorial`、`onboarding` | 功能说明书式 |
| 6 | 确认弹窗 | `confirm`、`Modal`、`Dialog` 内文案 | 「确认删除？」 |
| 7 | **标题 / 标签** | `<h1`-`<h3`、`<label`、`<span.*font-bold` | 「AI 提供商与模型设置」太正式 |
| 8 | **描述文案** | `<p` 标签内的说明文字 | 「选择您偏好的系统操作语言」 |
| 9 | **Placeholder** | `placeholder=` | 「请输入XX」「请输入您的」 |
| 10 | **警告 / 提示框** | `AlertCircle`、`alert`、`warning`、`Info` | 长段正式说明 |
| 11 | **状态标签** | `badge`、`status`、`tag`、`已连接` | 「已连接」「未连接」 |
| 12 | **表单标签** | `label`、`<span.*font-medium` | 「服务提供商」「模型选择」 |

**分析维度：**

| 维度 | 怎么判断 |
|------|----------|
| 人称 | 统计「你/您/用户」的使用频率 |
| 语气 | 文案是口语化还是书面化 |
| 情绪 | 错误时是安抚还是报告 |
| 幽默感 | 有没有俏皮话、双关、自嘲 |
| 专业度 | 用技术术语还是大白话 |

**输出示例：**

```json
{
  "voice": {
    "is": "从现有文案中提取的特征",
    "isNot": "从反面文案中推断的边界"
  },
  "copy": {
    "success": [
      { "bad": "原项目写的", "good": "保持原样或微调" }
    ]
  }
}
```

### 提取 shell.json（视觉外壳）

**前置条件：** 先有 soul.json。shell 的风格应由 soul 的人格推导而来。

**从 soul 推导 shell：**

| soul 特征 | shell 对应 |
|-----------|------------|
| 人格关键词（幽默/专业/温暖） | 色彩倾向、圆角大小、动效频率 |
| voice.is（轻松/严谨/有活力） | 字体选择、间距松紧、动画速度 |
| voice.isNot（不油腻/不冷冰冰） | 避免的视觉元素 |
| toneByScene（错误时温和） | 错误状态的色彩和动效 |
| referenceSentences 的感觉 | 整体视觉氛围 |

**推导逻辑：**

```
persona.keywords → color palette 倾向
  「幽默」→ 暖色、有活力的 accent
  「专业」→ 低饱和、冷静色调
  「温暖」→ 柔和阴影、圆角大

voice.is → typography + spacing
  「轻松」→ 字号适中、行高宽松
  「严谨」→ 字号精确、间距紧凑
  「有活力」→ 动效频率高、时长短

voice.isNot → antiPatterns
  「不油腻」→ 避免过度渐变、花哨装饰
  「不冷冰冰」→ 避免纯黑白、硬边角
```

**扫描目标：**

1. **全局样式** — 搜索 `globals.css`、`tailwind.config`、`theme` 文件
2. **CSS 变量** — 搜索 `--` 开头的自定义属性
3. **组件库** — 搜索 `components/ui`、`components/common` 目录
4. **Tailwind 配置** — 读 `tailwind.config.ts/js` 中的 theme 扩展
5. **动画定义** — 搜索 `@keyframes`、`transition`、`animation`
6. **布局结构** — 搜索 `Layout`、`Header`、`Sidebar`、`Footer` 组件

**分析维度：**

| 维度 | 怎么判断 |
|------|----------|
| 风格 | 圆角大小、阴影层次、色彩饱和度 |
| 密度 | 字号大小、间距紧凑度 |
| 动效 | 有没有动画、动画频率和类型 |
| 图标 | 用的什么图标库 |
| 弹窗 | 原生还是自定义 |

**输出示例：**

```json
{
  "tokens": {
    "color": {
      "primary": "从 CSS 变量或 tailwind config 中提取"
    },
    "radius": {
      "lg": "从实际组件的 rounded-* 类名中统计"
    }
  }
}
```

### 提取工作流

```
1. 扫描项目结构 → 定位关键文件
2. 收集文案样本 → 至少 20 条不同场景
3. 分析人称、语气、情绪、幽默感、专业度
4. 生成 soul.json 初稿 → 确定人格和 voice
5. 收集样式样本 → CSS 变量、Tailwind 类名、组件结构
6. 从 soul.json 推导 shell.json → 人格决定视觉
7. 用现有样式验证推导是否合理
8. 补充 antiPatterns（从现有代码的反面推断）
```

### 注意事项

- 提取的是「现状」，不是「理想状态」。可能需要在初稿基础上优化
- 如果项目有明确的 README 或文档，优先参考文档中的自我描述
- 如果项目没有任何风格倾向，可以参考同类产品的常见做法
- 提取完成后，用 audit 机制验证：现有文案是否通过阈值

---

## 框架演进

已吸收的外部经验：
- **brand-voice**：voice 的「是/不是」配对、语气光谱概念
- **brand-voice-architect**：内容审计评分机制、从已有内容反向提取
- **design-system-creation**：Token 三层架构思路
- **frontend-design-ultimate**：UI 反模式检测框架

可继续增加的维度：
- `accessibility` — 无障碍文案规范
- `i18n` — 多语言适配规则
- `onboarding` — 新手引导风格
- `notification` — 通知分级与文案
- `social` — 分享/协作场景的语气
