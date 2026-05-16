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

## 新增的 5 个学术版式概览

| ID | 用途 | 关键约束 |
|----|------|---------|
| S23 | Academic Data Table | 5-10 行 × 4-7 列学术度量表 |
| S24 | Half-Screen Figure | 左 50% 复合图 + 右 50% 解读列表 |
| S25 | Equation Hero | 中央 KaTeX 大公式 + 下方分项解释 |
| S26 | Tier Recommendation | 三列分层卡片（Primary / Alt / Fallback） |
| S27 | References | 参考文献列表（带编号） |

---

## S23 · Academic Data Table

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

**用途**：展示一条核心数学公式（例如 hybrid loss 定义）。

**适用内容**：可以用 KaTeX 渲染的 LaTeX 公式 + 下方 2-4 个分项解释（每项是公式中一个符号或一个组件）。

**关键类**：`.eq-hero`、`.eq-component-grid`、`.eq-component`、`.cite`

**视觉规则**：
- 公式居中显示，KaTeX `display` 模式（`$$ ... $$`）
- 公式上方有 1 行 t-meta kicker，下方有 1 行 lead 概念定义
- 下方 2-4 列 component grid，每列 = 一个符号 + 一句话解释
- 行间无线，纯 whitespace
- 公式字号通过 KaTeX 自身控制（默认 1.21em），Swiss 学术版强制 font-weight:300

**HTML 骨架**：
```html
<section class="slide light" data-layout="S25" data-animate="equation-reveal">
  <div class="canvas-card">
    <header class="chrome-min">...</header>
    <div style="flex:1;padding:0;display:grid;grid-template-rows:auto auto 1fr auto;gap:2.6vh">
      <div data-anim="kicker" class="t-meta">METHODOLOGY · LOSS FAMILIES · MULTIPLICATIVE HYBRID</div>
      <h2 data-anim="title" class="h-xl-zh" style="font-size:min(4.6vw,8.5vh);font-weight:200;line-height:1">
        乘法混合：方向门控 × 稳健幅度
      </h2>
      <div data-anim="equation" style="display:flex;flex-direction:column;justify-content:center;align-items:flex-start;padding:0 4vw">
        <div class="lead" style="margin-bottom:1.2vh">
          The multiplicative hybrid uses the directional penalty as a gating factor on the Huber backbone:
        </div>
        <div class="katex-display" style="font-size:1.4vw">
          $$L_{\mathrm{mul},\lambda}(y,\hat{y}) = \big(1 + \lambda \cdot D(y,\hat{y})\big) \cdot H_\delta(y - \hat{y}) \quad \text{where } D = \big[1 - \sigma(a y \hat{y})\big] \cdot \frac{|y|^b}{\mathbb{E}_{\text{batch}}[|y|^b] + \epsilon}$$
        </div>
      </div>
      <div data-anim="components" class="grid-12" style="gap:2vw;border-top:1px solid var(--border-subtle);padding-top:2vh">
        <div class="span-3">
          <div class="t-meta">λ · DIRECTIONAL WEIGHT</div>
          <div style="font-weight:300;font-size:1.4vh;margin-top:.6vh">控制方向门控强度。Phase 2 中 M1: λ=2, M2: λ=5</div>
        </div>
        <div class="span-3">
          <div class="t-meta">D · DIRECTIONAL GATE</div>
          <div style="font-weight:300;font-size:1.4vh;margin-top:.6vh">batch-normalised, 正确符号时 → 0，错误时 → +∞</div>
        </div>
        <div class="span-3">
          <div class="t-meta">H<sub>δ</sub> · HUBER BACKBONE</div>
          <div style="font-weight:300;font-size:1.4vh;margin-top:.6vh">δ=0.01；小残差二次、大残差线性，限制异常点影响</div>
        </div>
        <div class="span-3">
          <div class="t-meta">a, b · SHAPE PARAMS</div>
          <div style="font-weight:300;font-size:1.4vh;margin-top:.6vh">a=100 控制 sigmoid 陡峭度；b=2 放大大幅度回报权重</div>
        </div>
      </div>
    </div>
  </div>
</section>
```

**动效 recipe**：`equation-reveal` — kicker 与 title 一起 fade-up，公式从 0.95 → 1 scale-in 同时 opacity fade-in（450ms），下方四个 component 序列点亮。

**禁止**：
- 公式不能用 unicode 假装 LaTeX（如 `L = (1+λD)·H` 直接写在 HTML）—— 必须用 KaTeX `$$ ... $$`
- 不要让公式宽度超过 page width 90%（KaTeX 会自动 scroll，但视觉破碎）
- component grid 最多 4 列；超过用 S04 Six Cells

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
- [ ] 公式的字重通过 `.katex` 类被强制成 300（学术版 CSS 已处理）
- [ ] 行内公式不打断行高
- [ ] 长公式不超过 page width 90%，必要时换行或拆成两条

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
