# 事前登録: H4 下側クランプ（E1.4）

**固定日**: 2026-10-20（結果未見）
**仮説ID**: H4
**Status**: v0.2 draft. Supersedes `prereg_H4_clamp.md` (v0.1, kept for history).

## 目的
β の下側クランプ β_min を上げると、分散が縮み、lock-in 率が下がるかを検定する。

## 設計
- 基準セル: V=300, n_pairs=16, n_dist=32, d_mem ∈ {16, 32}, seed ∈ {0,1,2,3,4}。
- β_min ∈ {0, 0.25·mean, 0.5·mean}。
- **mean の定義**: 各 seed × d_mem の **β_min=0 run の実測平均 β**。この値を基準に 0.25×/0.5× を計算し、他水準に適用する（水準間で同じ絶対値を使う）。
- **t=0 は mean から除外**: t=0 は `e_prev=0 → k=0 → β=η/ε≈1e6` の無意味なスパイクで、平均を破壊する。実測では t≥1 の平均は 0.111。mean_s は t≥1 のみで計算する。
- 総 step 数は E1.1 と同一（1500）。
- lock-in の定義: acc < k × chance。k は H1 の Day 5 pilot 後に固定し、H4 の main run 前に適用する。

## 主エンドポイント（2端点、合取）
1. **分散端点**
   - Brown–Forsythe（中央値中心）で3水準の分散差を omnibus 検定。
   - 線形トレンド: 各水準の中央値からの絶対偏差を ordinal level（0,1,2）に回帰し、**負の傾き**を片側検定（α=0.05）。
   - 主判定は線形トレンド。Brown–Forsythe は omnibus の補助として報告。
2. **lock-in 端点**
   - Cochran–Armitage トレンド検定（3水準、ordinal score 0/1/2）で、β_min 増加に伴う lock-in 率の**減少**を片側検定（α=0.05）。
   - 2水準の Fisher 検定は記述的に併記（主判定には使わない）。

- **H4 成立** = 分散トレンド（負）と lock-in トレンド（負）の **両方** が α=0.05 で有意。
- **位置（平均 acc）の検定は禁止**：t 検定 / ANOVA / Mann–Whitney を使わない。平均の差はクランプの予測ではない。
- Brown–Forsythe は omnibus の **感度分析**、Fisher は 2×2 の **感度分析**（C0 vs C1 / C0 vs C2、Holm m=2）。どちらも主判定には使わない。

## collapse 0 件の事前規則
- 全水準で collapse 0 件の場合、Cochran–Armitage は退化（p=1）し、合取では H4 は成立しない。
- この場合「collapse なし」を p=1 として扱わず、**別の結果（floor effect）**として報告する。
- H4 の判定は **分散トレンド単独の副判定モード**に切り替える（事前登録済み）。lock-in 端点は「評価不能（全水準 0 件）」と明記し、H4 の成否に数えない。
- 副判定モード発動時は `master_log.md` に **「H4-副判定モード発動」** と明記する。
- 報告は「H4 が成立しなかった」ではなく、**「H4 主判定（合取）は不成立、lock-in は床効果で測定不能、分散トレンドは別途報告」**と区別する。
- collapse が 1 水準でも >0 なら、通常の合取判定（分散 + CA）を使う。

## 統計
- 主: `stats.linear_trend_dispersion`（分散、片側負トレンド）+ `stats.cochran_armitage_trend`（lock-in、片側負トレンド）。
- 感度: `stats.brown_forsythe`（omnibus）、`stats.fisher_exact_test`（2×2、C0 vs C1 / C0 vs C2、Holm m=2）。
- 位置検定（t / ANOVA / Mann–Whitney）は禁止。
- 2端点は合取なので、多重比較補正は適用しない（各 α=0.05）。

## 解釈制約
- β_min は順序尺度として扱う。水準間の等間隔性は仮定しない（score 0/1/2 は順序のみ）。
- 分散減少と lock-in 減少は異なる側面であり、片方だけでは H4 成立としない。
- 小規模 MQAR のみ。

## 結果記入欄（空欄で固定）
- mean β（β_min=0）:
- 分散: Brown–Forsythe p, trend slope, one-sided p
- lock-in: 率（3水準）, CA z, one-sided p
- 判定:
