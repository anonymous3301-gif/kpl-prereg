# 事前登録: H2 経路依存性（E1.2）

**固定日**: 2026-10-20（結果未見）
**仮説ID**: H2
**Status**: v0.5.1. Two primary contrasts, Holm m=2. Correction of v0.5: A vs B' is **not** exploratory; it is the H2-資源 primary contrast. D3 n=1 remains exploratory and is not a primary data point.

## 目的
A / B' / C の3経路で、次の2つを主対比として検定する。

- **H2-順序**: Δ_C − Δ_A。A と C は Kaczmarz 750 / const 750 が同一で、順序だけが異なる。順序は結果を変えるか。
- **H2-資源**: Δ_B' − Δ_A。総予算 1500 を「const 750 + Kacz 750」に使うか「Kacz 1500」に使うか。Kacz の step 数は資源か。

B' は「参照 arm」ではなく、**H2-資源の対比相手**である。参照と対比相手は別物。

## 設計
- 基準セル: V=300, n_pairs=16, n_dist=32, d_mem ∈ {16, 32}, seed ∈ {0,1,2,3,4}。
- 総 step 数は全経路 1500。
- 経路:
  - **A**: const 0.125 750 → keynorm Kaczmarz 750
  - **B'**: keynorm Kaczmarz 1500
  - **C**: keynorm Kaczmarz 750 → const 0.125 750
- 各段階の学習率・バッチサイズは E1.1 と同一。
- **no-write baseline**: メモリ書き込みを無効化し、state をゼロに固定したモデル。

## 主エンドポイント（2本、Holm m=2）
- 各 seed で Δ_A = acc(A) − acc(no-write)、Δ_B' = acc(B') − acc(no-write)、Δ_C = acc(C) − acc(no-write) を計算。
- **H2-順序**: Δ_C − Δ_A の両側 95% CI（seed で paired）。
- **H2-資源**: Δ_B' − Δ_A の両側 95% CI（seed で paired）。
- d_mem はプールする。各 seed で d_mem ごとの対比を平均し、seed で paired t 検定する。
- 2本とも主族として **Holm m=2** を適用する（小さい p を 0.025、大きい p を 0.05 と比較）。
- 各対比の未補正 95% CI と Holm 調整 p を併記する。
- d_mem 別の推定値と d_mem×経路の交互作用は記述的に報告する。

## 結果解釈の事前指定（結果を見る前に固定）
- **H2-資源 非有意 ∧ H2-順序 有意** → 「順序効果はあるが資源効果は検出できず」。H2-順序のみ支持。
- **H2-資源 有意（Δ_B' − Δ_A < 0、A > B'）** → 「Kacz step 数は資源ではない」。D3 の方向を支持。
- **H2-資源 有意（Δ_B' − Δ_A > 0、A < B'）** → 「資源仮説支持」。D3 の n=1 は外れ。
- **H2-順序 有意（Δ_C − Δ_A > 0）** → Kacz-first が良い。**有意（< 0）** → const-first が良い。
- 両方非有意 → 「順序効果も資源効果も検出されない」。
- δ は動かさない（等価性マージンは置かない）。

## D3/C/B' の n=1 観察（exploratory、主解析のデータ点ではない）
- D3 (A): acc=0.510、境界 E‖k‖²=10.91
- C: acc=0.062、E‖k‖²=15.04
- B': acc=0.4035、E‖k‖²=14.92
- const が E‖k‖² を 27% 縮め（15.04 → 10.91）、β を押し上げ、escape を越えさせたという機構注記は exploratory。
- **D3 単独は exploratory**。5 seed 本番の H2-資源のデータ点には含めない。5 seed で再現したら H2-資源の結果として扱う。

## 診断（exploratory、H2 main 前）
- **D1**: 750 Kacz only。acc=0.002、loss 5.7010 / 5.7050 / 5.6985（250/500/750 training steps）。
- **D2**: 750 norm（normbeta β≈1.0）+ 750 Kacz。norm の寄与の exploratory 記述用。pilot を止めない。
- **D3**: A 経路（750 const + 750 Kacz）。acc=0.510。1500 設計の成立根拠。
- 軌跡（seed 0, V=300, n=16, d_mem=16）: 250:5.7022 / 500:5.7091 / 750:5.7004 / 1000:5.6952 / 1250:5.5533 (acc 0.0450) / 1500:3.8945 (acc 0.4035)。**点火 ~1100–1200、加速は 1250 以降**。exploratory。

## 3000 設計の撤回
- D3（A, 1500 step）が acc=0.510 を示し、1500 で ignition が成立する以上、3000 に伸ばす理由がない。
- 3000 設計（A/B'/C 各 3000）は撤回する。

## 交絡の明記
- **H2-順序（Δ_C − Δ_A）**: A と C は Kaczmarz 750 / const 750 が同一で、順序だけが異なる。曝露が揃ったクリーンな対比。
- **H2-資源（Δ_B' − Δ_A）**: 総予算 1500 だが、Kaczmarz 曝露は B' 1500 / A 750。const priming の効果と追加 Kacz 750 step の効果を完全には分離しない。これは資源配分の対比として解釈する。
- H2-順序が有意でない場合も「path independent が証明された」とは書かず、「順序依存性は検出されない」と報告する。

## 解釈制約
- seed 5 の小規模。経路は A/B'/C に限定。
- D3/C/B' の n=1 観察は exploratory。5 seed の H2 主解析とは分離する。
- 「CI が 0 を含む」は「差がない」ことの証明ではない。
- 実モデルスケールへの外挿は非主張。
- norm 段階は V=300/n=16 で学習しないため設計から除外した（Day 2 の既知データ、normbeta η=1.0, loss≈5.73）。

## 結果記入欄（空欄で固定）
- Δ_A / Δ_B' / Δ_C:
- **H2-順序**: Δ_C − Δ_A（95% CI）, Holm adjusted p
- **H2-資源**: Δ_B' − Δ_A（95% CI）, Holm adjusted p
- d_mem 別:
- D3/C/B' n=1（exploratory）:
- 判定:
