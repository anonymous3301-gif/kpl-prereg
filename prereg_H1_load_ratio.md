# 事前登録: H1 load ratio

**固定日**: 2026-10-20（結果未見）

**Stage 1 frozen variant**: keynorm = normalized key direction + raw-key beta
(β = 1/‖k_raw‖²), i.e. the KLA Table 5 "key-norm only" ablation.
The exact raw-key KPL (β = η/(‖k‖²+ε), key_norm=False) is implemented and
unit-tested (T1–T5) but is **not** the Stage 1 arm. The "norm key + norm beta"
reading (β = η/(‖k_norm‖²+ε) ≈ η) is also implemented (`--variant normbeta`) but
is **not** the Stage 1 arm. This distinction is frozen before the E1.1 runs.

V = 300
chance = 1/300 = 0.003333...
n_special = 3
Q = 2000
collapse 閾値 = k × chance（k は Day 5 pilot 後に固定）

## 目的
H1（load ratio）の実験規模・chance 基準を凍結する。

## 前提
- n_max = 128（320 run 規模）
- assert: V >= 2 * n_max + n_special  -> 300 >= 256 + 3 (OK)
- logits 次元は V 全体なので chance = 1/V

## Cell membership（暫定、結果未見）
- (d_mem=16, n_pairs=16) は n/d=1 の容量境界セルであり、飽和した低負荷セルではない。
- Day 4–5 pilot（5 seed）で Kaczmarz–L2 の差が維持するかを見る。判定規則は動かさず、セル所属のみ可変とする。
- この暫定所属は E1.1 本番の前に固定する。

## E1.1 検定族（結果未見・起動前固定）

記号:
- `K` = keynorm Kaczmarz（Stage 1 frozen variant）。
- `L` = L2-const β=0.125。
- `acc(arm, n, d)` = 1500 step, Q=2000, V=300 の accuracy。
- Δ は seed で paired。

### C1′-NI（主、Holm 族=4 の一部）
- `n_pairs=32`: H0: acc(K,32,d) − acc(L,32,d) ≤ −0.02 を、片側95%下限 > −0.02 で棄却。
- `n_pairs=64`: 同様。
- d_mem は各セルで評価し、主検定は d_mem ごとの paired Δ を seed で平均して行う。
- 2本。

### C2′（主、Holm 族=4 の一部）
- d差: `D_arm(n) = acc(arm,n,d=16) − acc(arm,n,d=128)`。
- `C2′(n) = D_L(n) − D_K(n)`、片側95%下限 > 0（L2 の方が d_mem に敏感、という予測）。
- `n_pairs=32`, `64` の2本。

### Holm
- 族 = 4（C1′-NI ×2 + C2′ ×2）、α=0.05。

### C1′-EQ（族外）
- `n_pairs=32`, `64` で K vs L の TOST、δ=0.02（90% CI が ±0.02 内）。
- Holm 族には入れない。等価性の記述として報告。

### F1（d=128 での容量保持、guard-gated）
- `F1 = acc(K, n_pairs=128, d_mem=128) / acc(K, n_pairs=16, d_mem=128)`。
- 合格: `F1 ≥ 0.9`。
- ガード: `acc(K, n_pairs=16, d_mem=128) ≥ max(5/V, 0.10) = 0.10`。
- ガード外れ: `F1 = n/a`（検定せず、pass/fail に数えない）。
- L2 側の同じ比は記述的に併記。

### collapse / lock-in
- `collapse = acc ≤ k × chance`、`chance = 1/300`。
- k は Day 5 pilot 後に固定し、E1.1 本番前に適用する。
- `Q=2000`, `V=300`。

## n_test の定義と調整規則（2026-10-20、pilot_summary 前に凍結）

- **n_test** = C1′-NI に寄与する **paired seed/run の数**。`n_pairs`（32/64）でも、評価クエリ数 Q でも、bootstrap でもない。
- C1′-NI は `n_pairs=32` と `n_pairs=64` の各セルで、n_test 個の paired seed を使って検定する。
- `Q=2000` は1 run あたりの評価クエリ数で固定。bootstrap は sensitivity のみ。
- **SD_adj** = 4つの pilot セルにおける paired difference `d = acc_K − acc_L` の SD の最大値。pilot に n_pairs=32/64 が無いための保守的 proxy。**平均差 K−L は使わない**。
- 調整規則（SD_adj のみで決定）:
  - `SD_adj ≤ 0.05` → `n_test = 32`
  - `0.05 < SD_adj ≤ 0.10` → `n_test = 48`
  - `SD_adj > 0.10` → `n_test = 64`
- 上限は 64。SD_adj がこれを超えても n_test は 64 で固定する。
- 二峰性セル: 層別 SD が overall SD の 50% 未満なら、主解析の n_test は overall SD で決めた値のまま。層別 SD で再評価した n は **exploratory** として報告し、主解析には使わない。
- `δ=0.02` は固定。n_test の変更では動かさない。
- E1.1 の 320 run は base plan。C1′-NI セルは n_test に応じて run 数が増える。総 run 数はケンが再計算する。
- **凍結タイミングの注記**: この規則は pilot 中間観察（(128,16) の K−L 差と CI）を見た後に凍結した。規則は SD のみに基づき平均差を使わないが、完全な「結果前凍結」ではない。`master_log.md` に記録する。

## Pilot SD decomposition / n adjustment（E1.1 本番前）
- 全セルで SD を **overall** と **escape / non-escape 層別**に分解して報告する。
- escape の暫定ラベル: `acc ≥ 10 × chance = 0.0333`。感度分析として 5× / 20× も併記する。ラベルは記述用で、主解析のエンドポイントではない。
- 二峰性のセルでは overall SD だけでなく層別 SD を併記する。SD が escape の有無で支配される場合、n は事前登録の枠内で調整してよい（数値上書き）。**δ は動かさない**。
- robust SD（MAD）を sensitivity として併記する。
- 層別は exploratory であり、層別の仮説検定は行わない。

## 統計（予定）
- paired Δ + 両側 95% CI
- 非劣性片側 95%（C1′-NI）
- TOST（90% CI、C1′-EQ）
- RULER 3列出力（TOST90 / 非劣性片側 / 両側95%）
- Brown–Forsythe / Fisher / Holm（Holm 族=4）
- lock-in 率、collapse 判定
- F1 は guard 外れ時 `n/a`

## Day 2 implementation note（結果未見）
- 単体の Kaczmarz 射影は T1–T5 を通る（raw k, β=η/(‖k‖²+ε), ε=1e-6, η=1.0）。
- ただし V=300, d_mem=16, n_pairs=16 の 1500-step smoke では exact 射影 arm が chance 付近（0.0045）に留まった。Ablation: 同じ exact arm は V=64, n_pairs=8 で 0.7579、V=64, n_pairs=16 で 0.3770 まで学習する。したがって失敗は V=300 との組み合わせに固有で、key_norm=False 単独のせいではない。
- Stage 1 の実装 arm は key-norm-only Kaczmarz（正規化 k, β=1/‖k_raw‖²）を採用する。同じセルで 0.4035。
- この選択は結果を見る前の実装上の固定であり、H1 の主比較（Kaczmarz vs L2）は key-norm-only で行う。
