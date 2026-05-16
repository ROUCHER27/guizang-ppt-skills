# Layouts · Swiss 学术版扩展

> ⚠️ 此文件**只在使用 `template-swiss-academic.html` 时生效**。原版 `template-swiss.html` 不包含这些扩展类。
> 学术版继承原 Swiss 22 个版式 (S01-S22)，并新增 5 个学术专用版式 (S23-S27)。
> 完整原始版式定义参见 `layouts-swiss.md`。

---

## 学术版与原版的关系

| 资源 | 原 Swiss | 学术版 |
|------|----------|--------|
| 模板 | `assets/template-swiss.html` | `assets/template-swiss-academic.html` |
| 主题色 | `themes-swiss.md` (4 套) | `themes-swiss-academic.md` (4 套，继承 + 双色例外) |
| 版式 | `layouts-swiss.md` (S01-S22) | 本文件 (继承 S01-S22 + 新增 S23-S27) |
| 锁约束 | `swiss-layout-lock.md` | 同样适用，新增版式必须写 `data-layout` |
| 校验 | `validate-swiss-deck.mjs` | 同上（暂不强制校验新版式，未来可扩展） |

**版式选择原则**：
- 概念叙事页 → 优先用 S01-S22（保持 Swiss 极简感）
- 数据表格、公式、复合图 → 用 S23-S27 学术扩展
- 一份学术 deck 通常 60% 用原版式 + 40% 用扩展版式

---

## ⚠️ 常见陷阱（每次生成前先读）

这些是**已经踩过的坑**，按出现顺序排列。每条都有"症状"和"修复"两栏。

### 陷阱 1 · 公式根本没渲染（最容易踩）

**症状**：页面上看到原始 `$$L_{\mathrm{mul},\lambda}(y,\hat{y}) = ...$$` 文字而不是漂亮的数学符号。

**根因**：KaTeX CDN 的 `<link>` / `<script>` 标签上有伪造或错误的 `integrity` SRI 哈希，浏览器拒绝加载脚本。

**修复**：
- 在 `template-swiss-academic.html` 引入 KaTeX 时**不要写 `integrity` 属性**，除非已从 KaTeX 官方文档复制了真实哈希
- 当前模板已修正为无 SRI 形式，直接复制使用即可
- **每次生成完 deck 必须在浏览器实际打开**确认公式渲染成数学符号，不要只看 HTML 代码

### 陷阱 2 · S25 Equation Hero 把 component 做成了底部"footer 条"

**症状**：4 个 component（λ / D / H_δ / a, b）只占页面底部 ≤10vh，每张只有 1 行文字描述，视觉上比公式弱很多。

**根因**：误把 component 当成"公式注脚"，实际它们才是这一页的**核心目标**——让观众理解每个符号在公式里的角色。

**修复（已写入 S25 骨架）**：
- 4 个 component 必须占下方 35–45vh（约 40% 页面高度）
- 每张 card 必须包含：大字符号 + t-meta 标签 + **KaTeX 公式** + 简短说明 + 具体数值（mono）
- **绝对不要只写文字描述**——必须用 `$$ ... $$` 写出该符号的数学定义/取值范围/分段函数
- 公式必须**与论文 LaTeX 源对齐**（参见陷阱 4）

### 陷阱 3 · S01 Cover 的作者/导师信息塞在底部 footer 一行

**症状**：学术 deck 封面把"作者 · 导师"压成最底一行 mono 小字，与商业 deck 无异，缺少答辩 deck 应有的 metadata 仪式感。

**修复（见下方 "S01 · Academic Cover Variant"）**：
- 学术封面采用 "标题区 + 右侧 metadata aside" 双列布局
- 右侧栏用 `border-left: 1px solid` 与标题区做 hairline 分隔
- 4 段 metadata 上下排列：SPEAKER · PROGRAMME · SUPERVISOR · INSTITUTION
- 每段允许 1-2 行换行，学号用 mono、姓名用 sans-serif Light

### 陷阱 4 · 数学表达脱离论文原文

**症状**：自行用 unicode 或非正式符号写公式（如 `H_δ(e) = ½e²`、`σ(ayŷ)` 直接拼成字符串），与论文 chapter3 / appendix A 的 LaTeX 源不一致。

**修复**：
- 生成任何含公式的 slide 前，必须 `grep` 论文 `2253235_yirongyu_2026_Supplementary/latex/chapter3_methodology.tex` 与 `appendix_A_loss_definitions.tex` 确认精确公式
- 永远使用 KaTeX `$$ ... $$` 而非 unicode
- 使用 `\big[...\big]`、`\frac`、`\begin{cases}`、`\mathbb{E}`、`\sigma`、`\tfrac` 等学术标准宏

---

## 新增的 5 个学术版式概览

| ID | 用途 | 关键约束 |
|----|------|---------|
| S23 | Academic Data Table | 5-10 行 × 4-7 列学术度量表 |
| S24 | Half-Screen Figure | 左 50% 复合图 + 右 50% 解读列表 |
| S25 | Equation Hero | 中央 KaTeX 大公式 + 下方分项解释 |
| S26 | Tier Recommendation | 三列分层卡片（Primary / Alt / Fallback） |
| S27 | References | 参考文献列表（带编号） |

---

## S01 · Academic Cover Variant（封面学术变体）

**用途**：学术 deck（FYP 答辩 / viva / 学术报告）的封面，需要醒目展示作者、学号、Programme、导师、机构等元数据。

**与原 S01 IKB 默认变体的差异**：
- 标题区从单列改为 **双列 grid**：左列大标题、右列 metadata aside
- 右侧 aside 宽度由内容决定（`grid-template-columns: 1fr auto`），通过 `border-left: 1px solid` 与标题区分隔
- 元数据按学术答辩惯例分 4 段：**SPEAKER · PROGRAMME · SUPERVISOR · INSTITUTION**
- 学号用 `var(--mono)` 等宽字体，姓名用 `var(--sans)` Light（300）
- 每段允许 1-2 行换行（如 `BSc Applied<br/>Mathematics`、`XJTLU<br/>School of Math & Physics`）

**关键类**：与原 S01 完全相同（`.slide.accent`、`.ascii-bg`、`.t-meta`、`.lead`），只在标题 grid 加 `aside` 子元素。

**视觉规则**：
- aside 与标题底部对齐：`align-items: end`
- aside 与标题间距：`gap: 4vw`
- aside 内部 4 段间距：`gap: 1.6vh`
- 每段内部：t-meta 小标 + 主文 + （可选）补充行，`gap: .3vh`
- 颜色全部 `rgba(255,255,255,...)` 不同透明度（在 IKB 蓝底上反白）：
  - t-meta 标签：`.6` 不透明
  - 主文：`#fff` 全白
  - 学号、辅助行：`.78` ~ `.92` 之间

**HTML 骨架**：
```html
<section class="slide accent" data-animate="hero">
  <div class="canvas-card">
    <canvas class="ascii-bg" aria-hidden="true"></canvas>
    <div class="chrome-min">
      <div class="l">FYP DEFENSE · 2026.05.16</div>
      <div class="r">XJTLU MAP · 01 / NN</div>
    </div>
    <div style="flex:1;padding:0;display:grid;grid-template-rows:auto 1fr auto;gap:2.6vh">
      <div data-anim="kicker" class="t-meta" style="color:rgba(255,255,255,.78);letter-spacing:.22em">FINAL YEAR PROJECT · [SECTION EN]</div>

      <!-- 标题 + 右侧 metadata aside -->
      <div data-anim="title" style="align-self:center;display:grid;grid-template-columns:1fr auto;gap:4vw;align-items:end">
        <h1 style="font-family:var(--sans),var(--sans-zh);font-weight:200;font-size:min(7.4vw,12.4vh);line-height:.94;letter-spacing:-.025em;color:#fff;margin:0">
          [必填] Multiplicative<br/><span style="font-style:italic;font-weight:300">[必填] Italic 微强调</span><br/>[必填] 第三行
        </h1>

        <aside style="display:flex;flex-direction:column;gap:1.6vh;border-left:1px solid rgba(255,255,255,.32);padding:.5vh 0 .5vh 1.8vw;min-width:18ch">
          <div style="display:flex;flex-direction:column;gap:.3vh">
            <div class="t-meta" style="color:rgba(255,255,255,.6);letter-spacing:.22em">SPEAKER</div>
            <div style="font-family:var(--sans),var(--sans-zh);font-weight:300;font-size:min(1.9vw,3.2vh);color:#fff;line-height:1.15">[必填] 姓名</div>
            <div style="font-family:var(--mono);font-weight:400;font-size:min(1.1vw,1.9vh);color:rgba(255,255,255,.78);letter-spacing:.04em">[必填] 学号</div>
          </div>
          <div style="display:flex;flex-direction:column;gap:.3vh">
            <div class="t-meta" style="color:rgba(255,255,255,.6);letter-spacing:.22em">PROGRAMME</div>
            <div style="font-family:var(--sans),var(--sans-zh);font-weight:300;font-size:min(1.05vw,1.85vh);color:rgba(255,255,255,.92);line-height:1.3">[必填] 专业<br/>[可选] 第二行</div>
          </div>
          <div style="display:flex;flex-direction:column;gap:.3vh">
            <div class="t-meta" style="color:rgba(255,255,255,.6);letter-spacing:.22em">SUPERVISOR</div>
            <div style="font-family:var(--sans),var(--sans-zh);font-weight:300;font-size:min(1.45vw,2.4vh);color:#fff;line-height:1.2">[必填] Dr. 导师姓名</div>
          </div>
          <div style="display:flex;flex-direction:column;gap:.3vh">
            <div class="t-meta" style="color:rgba(255,255,255,.6);letter-spacing:.22em">INSTITUTION</div>
            <div style="font-family:var(--sans),var(--sans-zh);font-weight:300;font-size:min(1.05vw,1.85vh);color:rgba(255,255,255,.92);line-height:1.3">[必填] 学校缩写<br/>[必填] 学院全称</div>
          </div>
        </aside>
      </div>

      <div data-anim="bottom" style="display:grid;grid-template-rows:auto auto;gap:1.6vh;border-top:1px solid rgba(255,255,255,.22);padding-top:2vh">
        <div data-anim="lead" class="lead" style="max-width:62ch;color:rgba(255,255,255,.86);font-weight:300">[必填] 1-2 行的副标 / 一句话研究问题.</div>
        <div style="display:flex;justify-content:space-between;align-items:end">
          <div class="t-meta" style="color:rgba(255,255,255,.6)">[选填] 答辩信息 · 日期</div>
          <div class="t-meta" style="color:rgba(255,255,255,.6)">→ ARROW KEYS</div>
        </div>
      </div>
    </div>
  </div>
</section>
```

**禁止**：
- 不要把 metadata 缩到底部 footer 一行 mono（这是商业 deck 模式，学术答辩不适合）
- 学号与作者姓名必须放在同一段（视觉上"主讲人 + 学号"是一组关系）
- aside 内不能用 IKB 蓝 accent 强调任何元素（已经是 IKB 满底，蓝压蓝看不见）
- aside 总宽度建议 18-22ch，不要超过 25ch（防止挤占标题）

---



**用途**：展示多列学术度量表格，例如 baseline 损失对比、γ refinement、normalisation probe。

**适用内容**：5-10 行 × 4-7 列，每行一个 loss/variant，列为 Sharpe / CV / Cumulative / R² 等。

**关键类**：`.acad-table`、`.acad-row-best`、`.acad-row-second`、`.acad-table-caption`

**视觉规则**：
- 顶部 + 底部各一条 1.5px 黑色 hairline (booktabs 风格)
- 表头 1px hairline 下边框，行间无线（纯 whitespace 分隔）
- 数字列 `mono + tabular-nums` 防错位
- 第一列 (loss name) 左对齐 sans-serif，其他列右对齐 mono
- Best 行：左侧 IKB 短色块 + 加粗 + accent 颜色文字
- Second 行：左侧 1px hairline 短线（不强调）

**HTML 骨架**：
```html
<section class="slide light" data-layout="S23" data-animate="table-reveal">
  <div class="canvas-card">
    <header class="chrome-min">
      <div class="l">[必填] Deck 标题 · Issue/Field Note</div>
      <div class="r">[必填] 章节 · 页码 / NN</div>
    </header>
    <div style="flex:1;padding:0;display:grid;grid-template-rows:auto auto 1fr auto;gap:2.6vh">
      <div data-anim="kicker" class="t-meta">RESULTS · PHASE 1 · BASELINE COMPARISON</div>
      <h2 data-anim="title" class="h-xl-zh" style="font-size:min(5.2vw,9.2vh);font-weight:200;line-height:.95">
        七种基线损失下的<br/>样本外 Sharpe 对比
      </h2>
      <table class="acad-table" data-anim="table">
        <thead>
          <tr>
            <th>Loss</th>
            <th>Sharpe</th>
            <th>Cum. return</th>
            <th>Avg R²</th>
            <th>Avg monthly LS</th>
            <th>Monthly LS std</th>
          </tr>
        </thead>
        <tbody>
          <tr><td>MSE</td><td>−0.4643</td><td>−0.1125</td><td>−102.18</td><td>−0.004422</td><td>0.032989</td></tr>
          <tr><td>MedSE</td><td>0.0932</td><td>0.0060</td><td>−2.30 × 10⁶</td><td>0.001214</td><td>0.045124</td></tr>
          <tr><td>MADL</td><td>−0.3058</td><td>−0.0756</td><td>−4.15 × 10⁹</td><td>−0.002794</td><td>0.031653</td></tr>
          <tr class="acad-row-second"><td>GMADL</td><td>0.2025</td><td>0.0279</td><td>−7.02 × 10⁹</td><td>0.001429</td><td>0.024449</td></tr>
          <tr><td>IMADL</td><td>−0.3732</td><td>−0.0944</td><td>−106.51</td><td>−0.003578</td><td>0.033211</td></tr>
          <tr class="acad-row-best"><td>hybrid_mul_m1</td><td>0.4435</td><td>0.0509</td><td>−4.79</td><td>0.002215</td><td>0.017302</td></tr>
          <tr><td>hybrid_mul_m2</td><td>−0.0017</td><td>−0.0032</td><td>−1.03</td><td>−0.000008</td><td>0.016096</td></tr>
        </tbody>
      </table>
      <div class="acad-table-caption">
        SEED 42 · TRAIN 1990-01..1994-12 · TEST 1995-01..1996-12 · CAP05 · 24-MONTH OOS
      </div>
    </div>
  </div>
</section>
```

**动效 recipe**：`table-reveal` — 表头先 fade-in，行依次从上到下点亮（每行 60ms 间隔），best 行的 accent 短条最后弹入。

**禁止**：
- 不要给行加背景色 zebra-striping —— Swiss 不用色块表行分隔
- 不要给数字列加 grid 线（除非用 `with-row-rules` 类，仅在 8+ 行时启用）
- 一份 deck 的所有 acad-table 必须用统一字号（不要某页 1.4vh 某页 1.6vh）
- best/second 各一行就好，不要 3 行都标颜色

---

## S24 · Half-Screen Figure

**用途**：展示无法拆分的复合图（多 panel matplotlib 图、混合 bar+scatter 图等）。

**适用内容**：原图比例不是 21:9 / 16:10，但又必须保留全部 panel 信息。

**关键类**：`.half-fig-grid`（基于 `.grid-12`）、`.half-fig-readout`

**视觉规则**：
- 网格 7+5 或 8+4 split：左列大图、右列解读
- 图片容器允许 16:9 / 16:10 / 4:3 任意比例（不强制 21:9）
- 图片 `object-fit:contain`（保留全部内容，不裁剪）
- 右列解读用 3-5 个有序小条目，每条 1-2 行
- 图片下方可加 1 行 caption（mono uppercase 小字）

**HTML 骨架**：
```html
<section class="slide light" data-layout="S24" data-animate="head-grid">
  <div class="canvas-card">
    <header class="chrome-min">...</header>
    <div style="flex:1;padding:0;display:grid;grid-template-rows:auto 1fr;gap:2.6vh">
      <div data-anim="head" style="display:flex;flex-direction:column;gap:1vh">
        <div class="t-meta">RESULTS · PHASE 3a · γ REFINEMENT</div>
        <h2 class="h-xl-zh" style="font-size:min(4.6vw,8.5vh);font-weight:200">
          γ=0.7 在 Sharpe / 稳定性 / 波动率三维度同时取最优
        </h2>
      </div>
      <div class="grid-12" style="gap:2vw;align-items:start">
        <div class="span-7" style="display:flex;flex-direction:column;gap:1vh">
          <div class="frame-img" style="aspect-ratio:16/10;background:var(--paper);overflow:hidden">
            <img src="images/14-gamma-tuning-curve.png"
                 alt="γ tuning curve"
                 style="width:100%;height:100%;object-fit:contain;background:var(--paper)">
          </div>
          <div class="t-meta" style="font-size:.95vh;color:var(--text-secondary)">
            FIG 5.4 · γ TUNING CURVE · 3 SEEDS · CAP05
          </div>
        </div>
        <div class="span-5" style="display:flex;flex-direction:column;gap:2vh;padding-top:1vh">
          <div style="display:flex;flex-direction:column;gap:.6vh;border-top:1px solid var(--border-subtle);padding-top:1.2vh">
            <div class="t-meta">PANEL A · MEAN SHARPE</div>
            <div style="font-weight:300;font-size:1.5vh">在 γ=0.7~1.0 区间形成 plateau，γ=0.7 mean Sharpe = <strong style="color:var(--accent);font-weight:500">0.916</strong></div>
          </div>
          <div style="display:flex;flex-direction:column;gap:.6vh;border-top:1px solid var(--border-subtle);padding-top:1.2vh">
            <div class="t-meta">PANEL B · STABILITY</div>
            <div style="font-weight:300;font-size:1.5vh">CV 在 γ=0.7 取最小值 <strong style="color:var(--accent);font-weight:500">0.181</strong>，是 γ=1.0 的 1/3</div>
          </div>
          <div style="display:flex;flex-direction:column;gap:.6vh;border-top:1px solid var(--border-subtle);padding-top:1.2vh">
            <div class="t-meta">PANEL C · PORTFOLIO VOL</div>
            <div style="font-weight:300;font-size:1.5vh">月度 LS 标准差也在 γ=0.7 最小，三维同步</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</section>
```

**动效 recipe**：`head-grid` — 标题 fade-up，左列图 fade-in，右列三条解读依次序列点亮（120ms 间隔）。

**禁止**：
- 不要在图周围加阴影或圆角（保持 Swiss 直角）
- 不要让右列文字超过 5 条（信息密度过高）
- 图片必须 `object-fit:contain`，不能 `cover`（学术图不许裁剪）
- 配色：图本身可以用论文原色，但 chrome 和右列文字仍用单 accent

---

## S25 · Equation Hero

**用途**：展示一条核心数学公式（例如 hybrid loss 定义），并把公式中的每个符号 / 子项作为**主视觉锚点**展开。

**适用内容**：可以用 KaTeX 渲染的 LaTeX 公式 + 下方 3-4 个核心 component（每项必须包含该符号的**数学定义**或**取值范围**，不只是文字描述）。

**关键类**：`.katex-display`、`.t-meta`、`.grid-12`、`.span-3`、`.cite`

**视觉规则**：
- **页面纵向比例**：标题区 ~25vh / 公式区 ~25-30vh（紧凑双公式）/ component 区 ~40-45vh（**主视觉**）
- 公式区上下用 `border-top: 1px solid var(--border-subtle)` + `border-bottom: 1px solid var(--border-subtle)` hairline 包夹，与 component 区用空白自然分隔
- 标题字号比 S22 章节标题略小（`min(3.6vw,6.8vh)` 而非 `min(4.6vw,8.5vh)`），把空间让给公式与 component
- 4 个 component card 在 `grid-12` 上 4×`span-3`，`gap:0`，列与列之间用 `border-right: 1px solid var(--border-subtle)` 分隔（最后一列不加）
- **每张 card 的内部结构（必选）**：
  1. 大字符号（`min(4vw,7vh)`，`font-style:italic`，`font-weight:300`，第 1 张可用 accent 色）
  2. t-meta 角色标签（如 `DIRECTIONAL WEIGHT`）
  3. **KaTeX 公式块**（`$$ ... $$`，font-size 约 `.74vw - .82vw`，宽度 100%）—— **不可省略**
  4. 1-2 行简短文字说明（最多 2 行，font-size `1.3vh`）
  5. 底部 mono 数值块（具体参数 / 取值范围 / 论文常数），与上方用 `border-top: 1px solid var(--border-subtle)` + `padding-top:.9vh` 分隔，`margin-top: auto` 推到底
- 公式字号通过 KaTeX 自身控制；Swiss 学术版 CSS 强制 `.katex { font-weight:300 }`

**HTML 骨架**：
```html
<section class="slide light" data-layout="S25" data-animate="grid-reveal">
  <div class="canvas-card">
    <div class="chrome-min">...</div>
    <div style="flex:1;padding:0;display:grid;grid-template-rows:auto auto auto 1fr;gap:1.8vh">
      <div data-anim="kicker" class="t-meta">METHODOLOGY · LOSS FAMILIES · MULTIPLICATIVE HYBRID</div>
      <h2 data-anim="title" class="h-xl-zh" style="font-size:min(3.6vw,6.8vh);font-weight:200;line-height:1;color:var(--ink);margin:0">
        乘法混合：<span style="color:var(--accent);font-style:italic;font-weight:300">方向门控</span> × 稳健幅度
      </h2>

      <!-- 紧凑公式区，hairline 上下包夹 -->
      <div data-anim="equation" style="display:flex;flex-direction:column;justify-content:center;gap:1.2vh;padding:1vh 0;border-top:1px solid var(--border-subtle);border-bottom:1px solid var(--border-subtle)">
        <div class="katex-display" style="font-size:1.35vw;width:100%;margin:0">
          $$L_{\mathrm{mul},\lambda}(y,\hat{y}) \;=\; \big(1 + \lambda \cdot D(y,\hat{y})\big)\;\cdot\;H_\delta(y - \hat{y})$$
        </div>
        <div class="katex-display" style="font-size:1.05vw;width:100%;margin:0;color:var(--text-secondary)">
          $$\text{where}\quad D(y,\hat{y}) \;=\; \big[1 - \sigma(a\,y\,\hat{y})\big]\;\cdot\;\frac{|y|^{b}}{\mathbb{E}_{\mathrm{batch}}[|y|^{b}] + \epsilon}$$
        </div>
      </div>

      <!-- Component 网格 - 主视觉，占下方约 40vh -->
      <div data-anim="components" class="grid-12" style="gap:0;align-items:stretch">
        <!-- Card 1 · λ -->
        <div class="span-3" style="display:flex;flex-direction:column;gap:1vh;padding:2vh 1.2vw 0 0;border-right:1px solid var(--border-subtle)">
          <div style="font-family:var(--sans);font-style:italic;font-weight:300;font-size:min(4vw,7vh);line-height:1;color:var(--accent);letter-spacing:-.02em">λ</div>
          <div class="t-meta" style="color:var(--ink);letter-spacing:.18em">DIRECTIONAL WEIGHT</div>
          <div style="font-weight:300;font-size:1.3vh;color:var(--text-primary);line-height:1.4">Scalar coefficient on the gate; tunes how aggressively sign-wrong predictions are amplified.</div>
          <div class="katex-display" style="font-size:.78vw;margin:0;text-align:left;color:var(--ink)">
            $$\lambda \in \{0.1,\, 0.5,\, 2,\, 5\}$$
          </div>
          <div style="font-family:var(--mono);font-weight:400;font-size:1.2vh;color:var(--text-secondary);margin-top:auto;border-top:1px solid var(--border-subtle);padding-top:.9vh;line-height:1.5">
            M<sub>1</sub>: λ = 2<br/>M<sub>2</sub>: λ = 5
          </div>
        </div>

        <!-- Card 2 · D -->
        <div class="span-3" style="display:flex;flex-direction:column;gap:1vh;padding:2vh 1.2vw 0 1.2vw;border-right:1px solid var(--border-subtle)">
          <div style="font-family:var(--sans);font-style:italic;font-weight:300;font-size:min(4vw,7vh);line-height:1;color:var(--ink);letter-spacing:-.02em">D</div>
          <div class="t-meta" style="color:var(--ink);letter-spacing:.18em">DIRECTIONAL GATE</div>
          <div class="katex-display" style="font-size:.74vw;margin:0;text-align:left;color:var(--ink)">
            $$D(y,\hat{y}) \;=\; \big[1 - \sigma(a\,y\,\hat{y})\big]\cdot \frac{|y|^{b}}{\mathbb{E}_{\mathrm{batch}}[|y|^{b}] + \epsilon}$$
          </div>
          <div style="font-weight:300;font-size:1.3vh;color:var(--text-primary);line-height:1.4"><em>D</em> ≥ 0; <em>D</em> → 0 when sign(<em>y</em>) = sign(<em>ŷ</em>).</div>
          <div style="font-family:var(--mono);font-weight:400;font-size:1.2vh;color:var(--text-secondary);margin-top:auto;border-top:1px solid var(--border-subtle);padding-top:.9vh;line-height:1.5">
            range ≈ [0, 2]<br/>ε = 10<sup>−8</sup>
          </div>
        </div>

        <!-- Card 3 · H_δ -->
        <div class="span-3" style="display:flex;flex-direction:column;gap:1vh;padding:2vh 1.2vw 0 1.2vw;border-right:1px solid var(--border-subtle)">
          <div style="font-family:var(--sans);font-style:italic;font-weight:300;font-size:min(4vw,7vh);line-height:1;color:var(--ink);letter-spacing:-.02em">H<sub style="font-size:.45em;font-style:normal">δ</sub></div>
          <div class="t-meta" style="color:var(--ink);letter-spacing:.18em">HUBER BACKBONE</div>
          <div class="katex-display" style="font-size:.78vw;margin:0;text-align:left;color:var(--ink)">
            $$H_\delta(e) \;=\; \begin{cases} \tfrac{1}{2}\,e^{2} & |e| \le \delta \\[2pt] \delta\big(|e| - \tfrac{\delta}{2}\big) & |e| > \delta \end{cases}$$
          </div>
          <div style="font-weight:300;font-size:1.3vh;color:var(--text-primary);line-height:1.4">Quadratic for small residuals, linear above δ; <em>e</em> = <em>y</em> − <em>ŷ</em>.</div>
          <div style="font-family:var(--mono);font-weight:400;font-size:1.2vh;color:var(--text-secondary);margin-top:auto;border-top:1px solid var(--border-subtle);padding-top:.9vh;line-height:1.5">
            δ = 0.01
          </div>
        </div>

        <!-- Card 4 · a, b -->
        <div class="span-3" style="display:flex;flex-direction:column;gap:1vh;padding:2vh 0 0 1.2vw">
          <div style="font-family:var(--sans);font-style:italic;font-weight:300;font-size:min(4vw,7vh);line-height:1;color:var(--ink);letter-spacing:-.02em">a, b</div>
          <div class="t-meta" style="color:var(--ink);letter-spacing:.18em">SHAPE PARAMETERS</div>
          <div class="katex-display" style="font-size:.78vw;margin:0;text-align:left;color:var(--ink)">
            $$\sigma(a\,y\,\hat{y}) \;=\; \frac{1}{1 + e^{-a\,y\,\hat{y}}},\quad |y|^{b}$$
          </div>
          <div style="font-weight:300;font-size:1.3vh;color:var(--text-primary);line-height:1.4"><em>a</em> sets sigmoid steepness; <em>b</em> magnifies large-|<em>y</em>| weight.</div>
          <div style="font-family:var(--mono);font-weight:400;font-size:1.2vh;color:var(--text-secondary);margin-top:auto;border-top:1px solid var(--border-subtle);padding-top:.9vh;line-height:1.5">
            a = 100<br/>b = 2
          </div>
        </div>
      </div>
    </div>
  </div>
</section>
```

**动效 recipe**：`grid-reveal`（已存在于模板的 `RECIPES` 字典）—— kicker 与 title fade-up，公式 fade-in，4 个 component card 序列点亮（80-120ms 间隔）。

**禁止（强制）**：
- ❌ 公式不能用 unicode 假装 LaTeX（如 `H_δ(e) = ½e²` 直接写在 HTML）—— 必须用 KaTeX `$$ ... $$`
- ❌ Component card **必须含 KaTeX 公式块**，不能只用文字描述敷衍（这是这一页的核心目标）
- ❌ Component 区不能压缩到 ≤ 10vh 的"footer 条"，必须占下方 35-45vh
- ❌ 公式宽度不要超过 card 内宽 95%，避免 KaTeX 自动 scroll 导致版式破碎
- ❌ 公式字号超出 card 宽度时，先降字号 (`.7vw → .65vw`)，再考虑拆分公式（不要让公式换行折叠）
- ❌ 4 个 card 必须等高（`align-items:stretch`），mono 数值块用 `margin-top:auto` 推到底

---

## S26 · Tier Recommendation

**用途**：展示 3 个分层选项（如 Primary / Alternative / Fallback），强调主选 vs 备选的层级。

**适用内容**：3 个候选项，每项有名称 + 1-2 个核心数据 + 1 行 caveat。

**关键类**：`.tier-grid`、`.tier-card`、`.tier-primary`、`.tier-alt`、`.tier-fallback`

**视觉规则**：
- 三列等宽 grid（`grid-12` + `span-4` × 3）
- Primary 列：IKB 实色填充（`card-accent` style，反白文字）
- Alt 列：黑色实色填充（`card-ink`，反白文字）
- Fallback 列：白底 1px hairline 描边（`card-outlined`，黑字）
- 每张卡顶部 t-meta tier 标签 + 中段 loss ID 大字 + 下段 KPI mono + 底部 caveat 一行
- 三张卡高度统一，使用 `.tier-grid` 的 `align-items:stretch`

**HTML 骨架**：
```html
<section class="slide light" data-layout="S26" data-animate="tier-reveal">
  <div class="canvas-card">
    <header class="chrome-min">...</header>
    <div style="flex:1;padding:0;display:grid;grid-template-rows:auto auto 1fr;gap:2.6vh">
      <div data-anim="kicker" class="t-meta">CONCLUSION · FINAL RECOMMENDATION · 3-TIER</div>
      <h2 data-anim="title" class="h-xl-zh" style="font-size:min(4.6vw,8.5vh);font-weight:200;line-height:1">
        三层推荐：明确范围、可回退
      </h2>
      <div class="tier-grid grid-12" data-anim="tiers" style="gap:1.6vw;align-items:stretch">
        <!-- Primary -->
        <div class="span-4 tier-card tier-primary" style="background:var(--accent);color:#fff;padding:3vh 2vw;display:flex;flex-direction:column;gap:1.6vh">
          <div class="t-meta" style="color:rgba(255,255,255,.78);letter-spacing:.18em">PRIMARY · BEST SUPPORTED</div>
          <div style="font-family:var(--mono);font-weight:500;font-size:2.2vh;letter-spacing:-.01em;line-height:1.05">m2_robust_gamma07</div>
          <div style="display:flex;flex-direction:column;gap:.4vh;border-top:1px solid rgba(255,255,255,.3);padding-top:1.2vh;margin-top:auto">
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">SHARPE  <span style="font-weight:500">0.9156</span></div>
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">CV  <span style="font-weight:500">0.1808</span></div>
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">CUM RET  <span style="font-weight:500">+27.99%</span></div>
          </div>
          <div style="font-weight:300;font-size:1.25vh;color:rgba(255,255,255,.85);line-height:1.4">
            Stable under normalisation probe. No seed produced negative Sharpe.
          </div>
        </div>
        <!-- Alternative -->
        <div class="span-4 tier-card tier-alt" style="background:var(--ink);color:#fff;padding:3vh 2vw;display:flex;flex-direction:column;gap:1.6vh">
          <div class="t-meta" style="color:rgba(255,255,255,.78);letter-spacing:.18em">HIGH-RETURN · WITH CAVEAT</div>
          <div style="font-family:var(--mono);font-weight:500;font-size:2.2vh;letter-spacing:-.01em;line-height:1.05">m2_robust_gamma10</div>
          <div style="display:flex;flex-direction:column;gap:.4vh;border-top:1px solid rgba(255,255,255,.3);padding-top:1.2vh;margin-top:auto">
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">SHARPE  <span style="font-weight:500">1.0043</span></div>
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">CV  <span style="font-weight:500">0.5613</span> <span style="color:#FF6B35">(3× higher)</span></div>
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">CUM RET  <span style="font-weight:500">+23.68%</span></div>
          </div>
          <div style="font-weight:300;font-size:1.25vh;color:rgba(255,255,255,.85);line-height:1.4">
            Highest Sharpe but seed-sensitive. Drops to 0.41 under normalisation.
          </div>
        </div>
        <!-- Fallback -->
        <div class="span-4 tier-card tier-fallback" style="background:var(--paper);color:var(--ink);padding:3vh 2vw;border:1px solid var(--ink);display:flex;flex-direction:column;gap:1.6vh">
          <div class="t-meta" style="color:var(--text-secondary);letter-spacing:.18em">STABLE FALLBACK</div>
          <div style="font-family:var(--mono);font-weight:500;font-size:2.2vh;letter-spacing:-.01em;line-height:1.05">imadl_m2_alpha06</div>
          <div style="display:flex;flex-direction:column;gap:.4vh;border-top:1px solid var(--ink);padding-top:1.2vh;margin-top:auto">
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">SHARPE  <span style="font-weight:500">0.6895</span></div>
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">CV  <span style="font-weight:500">0.2443</span></div>
            <div style="font-family:var(--mono);font-weight:300;font-size:1.5vh">CUM RET  <span style="font-weight:500;color:var(--accent)">+30.42%</span></div>
          </div>
          <div style="font-weight:300;font-size:1.25vh;color:var(--text-secondary);line-height:1.4">
            Independent corroboration. Highest cumulative return of three.
          </div>
        </div>
      </div>
    </div>
  </div>
</section>
```

**动效 recipe**：`tier-reveal` — 三张卡序列点亮，从 Primary → Alt → Fallback（200ms 间隔），每张卡内的 KPI 数字最后 fade-in。

**禁止**：
- 三张卡必须高度严格相等（用 `align-items:stretch`，不要硬编码 height）
- Primary 必须用 IKB accent 实色（不能用 outlined）—— 这是版式的层级语义
- 不要给 fallback 卡加阴影或圆角（保持 Swiss）
- 每张卡 KPI 不超过 4 行；caveat 不超过 2 行

---

## S27 · References

**用途**：参考文献页（defense 通常需要单独 1 页放完整 references）。

**适用内容**：3-15 条参考文献，每条 = 编号 + 作者 + 年份 + 标题（斜体）+ 期刊/出版商。

**关键类**：`.bib-list`、`.bib-line`、`.bib-num`、`.bib-body`

**视觉规则**：
- 单列 bibliography 列表，每条之间用 1px hairline 分隔
- 编号用 mono `[1]` 格式，宽度固定 3vw
- 标题用 italic（参照 IEEE/APA 学术惯例）
- 每条 1-2 行，超过 2 行时优先简化或拆成两条
- 字号偏小 (1.3vh)，强调密度而非展示性

**HTML 骨架**：
```html
<section class="slide light" data-layout="S27" data-animate="bib-reveal">
  <div class="canvas-card">
    <header class="chrome-min">...</header>
    <div style="flex:1;padding:0;display:grid;grid-template-rows:auto auto 1fr;gap:2.6vh">
      <div data-anim="kicker" class="t-meta">REFERENCES · CITED IN THIS PRESENTATION</div>
      <h2 data-anim="title" class="h-xl-zh" style="font-size:min(4.6vw,8.5vh);font-weight:200">
        References
      </h2>
      <div data-anim="bib" class="bib-list" style="overflow-y:auto;max-height:60vh;padding-right:1vw">
        <div class="bib-line">
          <div class="bib-num">[1]</div>
          <div class="bib-body">Gu, S., Kelly, B., &amp; Xiu, D. (2020). <em>Empirical asset pricing via machine learning</em>. The Review of Financial Studies, 33(5), 2223–2273.</div>
        </div>
        <div class="bib-line">
          <div class="bib-num">[2]</div>
          <div class="bib-body">Huber, P. J. (1964). <em>Robust estimation of a location parameter</em>. The Annals of Mathematical Statistics, 35(1), 73–101.</div>
        </div>
        <div class="bib-line">
          <div class="bib-num">[3]</div>
          <div class="bib-body">Michankow, J., Slepaczuk, R., &amp; Bielak, P. (2024). <em>Mean Absolute Directional Loss as a new loss function for stock price prediction</em>. Journal of Computational Science, 76, 102228.</div>
        </div>
        <div class="bib-line">
          <div class="bib-num">[4]</div>
          <div class="bib-body">Daniel, K., &amp; Moskowitz, T. J. (2016). <em>Momentum crashes</em>. Journal of Financial Economics, 122(2), 221–247.</div>
        </div>
        <div class="bib-line">
          <div class="bib-num">[5]</div>
          <div class="bib-body">Medhat, M., &amp; Schmeling, M. (2021). <em>Short-term momentum</em>. Review of Financial Studies, 35(3), 1480–1526.</div>
        </div>
      </div>
    </div>
  </div>
</section>
```

**动效 recipe**：`bib-reveal` — 编号 fade-in 后正文向左滑入（80ms 间隔逐条）。

**禁止**：
- 编号不要用 `1.` `2.` 而是 `[1]` `[2]`（学术惯例）
- 文献条目超过 8 条时强制 `overflow-y:auto`，不要让内容超出 nav 安全区
- 不要在 references 页加 illustration 或图标（纯文字）
- 一条 reference 超过 2 行视觉上太重，优先简化标题副标

---

## 学术版式决策树（生成 deck 前先判断）

```
slide 内容是?
├── 标题/封面/章节幕封      → S01 Cover / S03 Statement / S09 Dot Matrix
├── 一段文字论点            → S03 / S08 / S13
├── 一条核心数学公式        → S25 Equation Hero  ★ 学术版
├── 多个公式或定义           → S04 Six Cells / S05 Three Layers
├── 数据表格 (5+ 行 4+ 列)   → S23 Academic Data Table  ★ 学术版
├── 一张论文复合图 (多 panel) → S24 Half-Screen Figure  ★ 学术版
├── 一张 21:9 单图 (照片/数据视觉) → S22 Image Hero
├── 多图 (2-6 张)            → S15 Matrix / S16 Multi-card Brief
├── 时间线/迭代历史           → S02 Vertical Timeline / S11 Horizontal Timeline
├── 比较两个状态 (before/after) → S08 Duo Compare
├── 推荐 3 个分层选项         → S26 Tier Recommendation  ★ 学术版
├── 流程图/系统图             → S14 Loop / S17 System Diagram
├── KPI 大字/数据驱动         → S06 KPI Tower / S07 H-Bar Chart / S20 Stacked Ledger
├── 文献综述                  → S03 / S08（用 .cite 标注引用）
└── 参考文献页                → S27 References  ★ 学术版
```

---

## 学术 deck 节奏建议（15-20 页防御 deck）

| 阶段 | 推荐版式 | 数量 |
|------|---------|------|
| 开场 | S01 Cover | 1 |
| Outline | S03 / S04 | 1 |
| Motivation | S22 + S03 + S25 | 2-3 |
| Literature | S03 + S08（带 .cite） | 1-2 |
| Methodology | **S25** + S22 + S15 + S14 | 3-4 |
| Data | S22 / S06 / S20 | 1-2 |
| Results | **S23** + **S24** + S22 + S15 | 5-6 |
| Conclusion | **S26** + S03 | 1-2 |
| Limitations | S04 / S03 | 1 |
| Thank You | S10 Closing | 1 |
| References (backup) | **S27** | 1 |

加粗的版式来自学术扩展（S23-S27），其余继承自原 Swiss。

**节奏硬规则**（继承自原 Swiss）：
- 不允许连续 3 页同一主体结构（如连续三页 S23 Table）
- 每 3-4 页插入 1 个 hero 页（S01/S03/S22/S25）做呼吸
- 8 页以上必须有 ≥1 个 dark 页（S03/S08/S26 Primary 列）+ ≥1 个 light 页

---

## 学术内容的引用规则

**行内引用**（用 `.cite` 类）：
```html
<p>Gu et al.<span class="cite">[1]</span> showed deep models systematically outperform...</p>
```

渲染效果：超小号 mono `[1]` 上标，颜色比正文淡一档。

**重要引用强调**（用 `.cite-accent`）：
```html
<p>The MADL formulation<span class="cite cite-accent">[3]</span> is the foundation of...</p>
```

渲染时编号用 IKB 蓝（用于强调"这是关键文献"）。

**bib 编号必须与 S27 References 页对齐**：deck 里所有 `.cite` 引用编号必须能在 S27 找到对应条目。

---

## 学术版自检清单

生成完一个学术 deck 后逐项检查：

### 公式
- [ ] 所有公式用 KaTeX `$$ ... $$` 渲染，不是 unicode 假装
- [ ] 所有公式与论文 LaTeX 源对齐（grep `chapter3_methodology.tex` 与 `appendix_A_loss_definitions.tex`）
- [ ] **必须在浏览器实际打开 deck**确认公式渲染成数学符号，不只是看 HTML 代码
- [ ] CDN script 标签上**没有伪造的 `integrity` SRI 哈希**
- [ ] KaTeX 公式的字重通过 `.katex` 类被强制成 300（学术版 CSS 已处理）
- [ ] 行内公式不打断行高
- [ ] 长公式不超过 page width 90%，必要时换行或拆成两条
- [ ] S25 中**每张 component card 必须含 KaTeX 公式块**，不能只用文字描述

### 表格
- [ ] 用 `.acad-table` 而不是手写 `<table>` 加 inline style
- [ ] 数字列用 mono + tabular-nums（已通过 CSS 处理）
- [ ] best 行和 second 行不超过各 1 行
- [ ] 表格上下用 1.5px 黑线，行间无线
- [ ] 表格下方有 caption strip

### 图表
- [ ] 单 panel 图用 S22（21:9）
- [ ] 多 panel 复合图用 S24（保留全部 panel，不裁）
- [ ] 图周围无阴影、无圆角、无外框
- [ ] 复合图的 caption 在图下方，不在图上方
- [ ] 图的内部配色可以用论文原色，但 chrome / footer / kicker 仍只用一个 accent

### 引用与文献
- [ ] 所有引用用 `.cite` 类，编号格式 `[N]`
- [ ] 关键文献用 `.cite-accent`（可选）
- [ ] References 页放在主 deck 末尾或 backup 第一张
- [ ] References 页超过 8 条时启用 `overflow-y:auto`

### 推荐与结论
- [ ] 三层推荐用 S26 Tier Recommendation
- [ ] Primary 必须 IKB 实色，Fallback 必须 outlined
- [ ] 每张卡 KPI ≤ 4 行，caveat ≤ 2 行

### 整体
- [ ] 学术 deck 节奏遵循上面的"阶段 → 版式 → 数量"建议
- [ ] 不允许连续 3 页同结构
- [ ] 双色例外（best vs second）只用在 chart 内，chrome 仍单 accent
- [ ] 最后一页是 S10 Closing 或 S26 Tier，不要随便用 hero 收尾
