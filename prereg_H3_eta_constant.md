# 事前登録: H3 η の定数化（E1.3）

**固定日**: 2026-10-20（結果未見）
**仮説ID**: H3
**Status**: v0.2 draft. Supersedes `prereg_H3_eta.md` (v0.1, kept for history).

## 目的
Kaczmarz の relaxation η を定数 {1.0, 0.5, 0.125} に固定しても、学習可能な η_learned と同等かを TOST で判定する（H3-主）。
H3-副では、keynorm Kaczmarz と L2-const の置換可能性が予算に依存するかを非劣性で判定する。

## 設計
- 基準セル: V=300, n_pairs=16, n_dist=32, d_mem ∈ {16, 32}, seed ∈ {0,1,2,3,4}。
- η_const ∈ {1.0, 0.5, 0.125} の3水準 + η_learned の4条件。
- 総 step 数は E1.1 と同一（1500）。
- η_learned は sigmoid 出力（GDN と同様）。η_const は固定値。
- learned-mean の固定は **seed 0 のみ** から行う（他 seed への情報漏れ防止）。

## 主エンドポイント
- 各 η_const 水準 vs η_learned の **paired TOST、δ=0.01**（90% CI が ±0.01 内）。
- 主判定は **3本の合取（AND）**：3水準すべてが TOST を通過したとき H3 成立。
  - 合取（intersection-union）なので、主検定に Holm は適用しない。
  - Holm 補正後の p 値は **保守的な感度分析** として併記する（主判定には使わない）。
- 追加ガード（記述的）: learned run の **run 内 η_t の SD の中央値** が、最小水準間隔 0.125 より小さいこと。
  - 満たさない場合、H3 の主張を「acc 等価のみ」に格下げして報告する。

## 副次
- post-hoc swap（η_learned の平均を定数として評価）は **副証拠**。主証拠は再訓練 arm。
- 水準ごとの acc、TOST p、90% CI、η_t の SD を報告。

## 統計
- paired TOST（`stats.tost`）。
- 合取判定。Holm は感度分析（`stats.holm`）。
- δ=0.01 は main run 前に固定。pilot で SD を見て δ を変更する場合は、main run 前に amendment として理由と数値を記録する（pilot は主検定に使わない）。

## 解釈制約
- 「3水準すべて等価」は δ=0.01 の範囲での主張。δ を超える差の不存在は主張しない。
- η_t の SD ガードは記述的であり、formal test ではない。
- 小規模 MQAR のみ。

## H3-副: keynorm vs L2、予算依存（再定義、2026-10-20, main run 前）

**目的**: Kaczmarz ゲートの置換可能性を「予算依存」として検定する。
**除外**: normbeta η=0.125 vs L2-const は構成上同一（正規化 k, β≈η=0.125）なので、H3-副から除外する。smoke でも両者 0.1030 で一致した。

### 設計（新規 20 run、1500 側は E1.1 から抽出）
- arm: **keynorm Kaczmarz**（Stage 1 frozen）vs **L2-const β=0.125**。
- d_mem: {16, 32}。
- steps: {1500, 3000}。
- **1500-step 側**: E1.1 の該当セル（d_mem {16,32}, n_pairs=16）を subset。**追加 run ゼロ、10 seed**。
- **3000-step 側**: 新規 20 run（2 arms × 2 d_mem × 5 seeds、seed 0–4）。
- 交互作用 `Δ_1500 − Δ_3000` は **seed 0–4 で paired**（1500 側は 10 seed のうち 0–4 を使用）。
- 実測 150 sec/run（1500 step, n=16, CPU）。3000 step は約 2 倍。新規 20 run、4 並列で約 25 分。
- **一般化の限界**: 非劣性の主張は d_mem {16, 32} に限定する。d ≥ 64 への一般化は open。E1.1 は d=64/128 も回すが、H3-副の NI には含めない。

### 主エンドポイント（各予算）
- 非劣性: `acc(L) − acc(K) > −0.02` を片側95%下限で判定（L2 が K より δ=0.02 以上悪くないか）。
- 予測:
  - **1500 step**: 非劣性は成立しない（K > L、L2 が δ 以上悪い）。低予算ではゲートが効く。
  - **3000 step**: 非劣性が成立（K ≈ L、L2 は δ 以上悪くない）。高予算では L2 で置換可能。
- TOST は使わない（等価性の主張ではない）。

### 副エンドポイント
- 予算 × 腕の交互作用: `Δ_1500 − Δ_3000 > 0`（Δ = acc(K) − acc(L)、差が縮む）を片側検定。
- d_mem 別は記述的。

### 論文の主張文（更新）
> KPL の Kaczmarz ゲートは低予算での最適化であり、3000 step 以上では L2 固定 β で置換可能。これは 2507.06457 の LM 平坦性と整合し、KPL を「万能部品」ではなく「予算制約下の設計原理」として位置づける。

### 除外の記録
- normbeta η=0.125 vs L2-const は恒等式（正規化 k, β≈η=0.125）。20-run 比較は zero-power なので実施しない。

## 結果記入欄（空欄で固定）
- η=1.0 vs learned: Δ, 90% CI, TOST p
- η=0.5 vs learned: Δ, 90% CI, TOST p
- η=0.125 vs learned: Δ, 90% CI, TOST p
- 合取判定:
- η_t SD ガード:
- H3-副 1500（E1.1 subset, 10 seed）: Δ=acc(K)−acc(L), NI lower95, 成立/不成立
- H3-副 3000（新規 20 run, seed 0–4）: Δ=acc(K)−acc(L), NI lower95, 成立/不成立
- 交互作用 Δ_1500 − Δ_3000（seed 0–4）: 点推定, 片側 p
- 判定:
