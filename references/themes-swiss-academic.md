# Themes · Swiss 学术版主题色

> 学术版**完全继承**原 Swiss 的 4 套主题色（IKB / 柠檬黄 / 柠檬绿 / 安全橙）。
> 完整定义参见 `themes-swiss.md`，本文件只补充学术版独有的双色例外条款。

---

## 主题继承

学术版 `template-swiss-academic.html` 的 `:root` 块与 `template-swiss.html` 完全一致。切换主题的方式相同：

| # | 主题 | 锚点色 | 推荐场景（学术） |
|---|------|--------|------------------|
| 1 | 🔵 克莱因蓝 IKB | `#002FA7` | **学术 deck 默认**（financial / quant / 数据驱动） |
| 2 | 🟡 柠檬黄 | `#FFD500` | 不推荐用于学术 deck（饱和度过高，与黑文字对比生硬） |
| 3 | 🟢 柠檬绿 | `#C5E803` | 生态、可持续主题的学术研究 |
| 4 | 🟠 安全橙 | `#FF6B35` | 警示性主题（如风险研究、危机分析） |

**学术 deck 默认推荐 IKB**，理由：
- 历史文献（如经济学、运筹学）中 IKB 蓝是惯用色
- 与黑色正文 + 白底的对比最稳健
- 与论文 matplotlib 图的传统配色（深蓝/灰）兼容性最好

---

## 双色例外条款（学术版独有）

### 原 Swiss 规则

> "一份 deck 不能同时出现多个高亮色。Slide 的 chrome 与正文必须只用一个 accent。"

### 学术版扩展

学术 deck 经常需要在**图表内部**比较 best / second / third，单一 accent 无法表达层级。因此学术版**在图表内部允许**使用 2 色序列：

| 角色 | 颜色 | CSS 变量 |
|------|------|---------|
| Best | accent (IKB) | `var(--accent)` |
| Second | ink (黑) | `var(--ink)` |
| Third+ | text-secondary (灰) | `var(--text-secondary)` |

**触发方式**：在 chart 容器加 `chart-dual-accent` 类。

### 适用范围（强制）

- ✅ S23 Academic Data Table 的 best/second 行高亮
- ✅ S24 Half-Screen Figure 的右列 strong 数字
- ✅ S15/S16/S22 内部 chart（如 bar chart 高亮）
- ✅ S07 H-Bar Chart 的 winner bar
- ✅ S26 Tier Recommendation 的 Primary vs Alt vs Fallback 三色（已是版式内置）

### **不**适用范围（保持单 accent）

- ❌ chrome-min（页眉左/右文字）
- ❌ kicker（t-meta 小标）
- ❌ footer / caption / footnote
- ❌ icon / lucide 图标
- ❌ 装饰元素（dot-mat, ring-mat, hairline）

**核心思路**：信息层级允许双色，**装饰层级保持单色**。这是学术严谨性与 Swiss 美学的折中。

---

## 主题切换实操

### IKB（默认，无需修改）

`template-swiss-academic.html` 的 `:root` 块默认就是 IKB。如果 deck 是默认 IKB，跳过此步骤。

### 切换到其他主题

打开 `themes-swiss.md`，找到对应主题的 `:root` 块，**整体替换** `template-swiss-academic.html` 中 `:root{` 块的相应行（标有"主题色"注释的那几行）。

学术版的 `:root` 与原版完全相同，所以原版的主题切换流程 100% 适用。

---

## 学术 deck 主题选择决策

```
deck 主题是?
├── 量化金融 / 经济学 / 运筹            → 🔵 IKB（默认）
├── ML 应用研究 / 数据科学              → 🔵 IKB（默认）
├── 物理 / 化学 / 工程                  → 🔵 IKB（默认）
├── 生态 / 可持续 / 环境科学            → 🟢 柠檬绿
├── 风险研究 / 危机分析 / 警示性主题    → 🟠 安全橙
└── 其他人文 / 社会科学                 → 🔵 IKB（默认）/ 灰阶变体（手动）
```

**默认推荐 IKB**：除非主题强烈不匹配，否则一律 IKB。学术世界对蓝色有天然信任。

---

## 与原版的差异（一句话总结）

| 维度 | 原 Swiss | 学术版 |
|------|----------|--------|
| 主题色数量 | 4 套 | 4 套（继承） |
| 主题色定义 | 同 | 同 |
| 高亮规则 | 单 accent 全局 | 单 accent 全局 + **图表内部双色例外** |
| 默认推荐 | 4 套等权 | **IKB 优先** |
