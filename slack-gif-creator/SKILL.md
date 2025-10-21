---
name: slack-gif-creator
description: Slackに最適化されたアニメーションGIFを作成するためのツールキット。サイズ制約のバリデータと組み合わせ可能なアニメーションプリミティブを提供します。このスキルは、ユーザーが「XがYをしているSlack用のGIFを作って」といったリクエストでSlack用のアニメーションGIFや絵文字アニメーションを求める際に適用されます。
license: 完全な条件はLICENSE.txtに記載
---

# Slack GIF Creator - フレキシブルツールキット

Slack に最適化されたアニメーション GIF を作成するためのツールキットです。Slack の制約に対するバリデータ、組み合わせ可能なアニメーションプリミティブ、オプションのヘルパーユーティリティを提供します。**これらのツールをクリエイティブなビジョンを実現するために必要に応じて適用してください。**

## Slack の要件

Slack は用途に応じて GIF に特定の要件があります：

**メッセージ GIF:**

- 最大サイズ: 約 2MB
- 最適な寸法: 480x480
- 一般的な FPS: 15-20
- 色数制限: 128-256
- 長さ: 2-5 秒

**絵文字 GIF:**

- 最大サイズ: 64KB（厳密な制限）
- 最適な寸法: 128x128
- 一般的な FPS: 10-12
- 色数制限: 32-48
- 長さ: 1-2 秒

**絵文字 GIF は難易度が高い** - 64KB の制限は厳格です。役立つ戦略:

- フレーム数を合計 10-15 フレームに制限
- 最大 32-48 色を使用
- デザインをシンプルに保つ
- グラデーションを避ける
- ファイルサイズを頻繁に検証

## ツールキットの構造

このスキルは 3 種類のツールを提供します：

1. **バリデータ** - GIF が Slack の要件を満たしているかチェック
2. **アニメーションプリミティブ** - 動きの組み合わせ可能なビルディングブロック（シェイク、バウンス、移動、万華鏡）
3. **ヘルパーユーティリティ** - 一般的なニーズに対応するオプション機能（テキスト、色、エフェクト）

**これらのツールの適用方法については完全なクリエイティブの自由があります。**

## コアバリデータ

GIF が Slack の制約を満たしていることを確認するために、これらのバリデータを使用します：

```python
from core.gif_builder import GIFBuilder

# GIFを作成した後、要件を満たしているかチェック
builder = GIFBuilder(width=128, height=128, fps=10)
# ... 任意の方法でフレームを追加 ...

# 保存してサイズをチェック
info = builder.save('emoji.gif', num_colors=48, optimize_for_emoji=True)

# saveメソッドはファイルが制限を超えている場合に自動的に警告します
# info辞書の内容: size_kb, size_mb, frame_count, duration_seconds
```

**ファイルサイズバリデータ**:

```python
from core.validators import check_slack_size

# GIFがサイズ制限を満たしているかチェック
passes, info = check_slack_size('emoji.gif', is_emoji=True)
# 戻り値: (True/False, サイズ詳細の辞書)
```

**寸法バリデータ**:

```python
from core.validators import validate_dimensions

# 寸法をチェック
passes, info = validate_dimensions(128, 128, is_emoji=True)
# 戻り値: (True/False, 寸法詳細の辞書)
```

**完全な検証**:

```python
from core.validators import validate_gif, is_slack_ready

# すべての検証を実行
all_pass, results = validate_gif('emoji.gif', is_emoji=True)

# または簡易チェック
if is_slack_ready('emoji.gif', is_emoji=True):
    print("アップロード準備完了！")
```

## アニメーションプリミティブ

これらは動きのための組み合わせ可能なビルディングブロックです。任意のオブジェクトに任意の組み合わせで適用できます：

### シェイク（揺れ）

```python
from templates.shake import create_shake_animation

# 絵文字を揺らす
frames = create_shake_animation(
    object_type='emoji',
    object_data={'emoji': '😱', 'size': 80},
    num_frames=20,
    shake_intensity=15,
    direction='both'  # または 'horizontal', 'vertical'
)
```

### バウンス（跳ねる）

```python
from templates.bounce import create_bounce_animation

# 円を跳ねさせる
frames = create_bounce_animation(
    object_type='circle',
    object_data={'radius': 40, 'color': (255, 100, 100)},
    num_frames=30,
    bounce_height=150
)
```

### スピン / 回転

```python
from templates.spin import create_spin_animation, create_loading_spinner

# 時計回りのスピン
frames = create_spin_animation(
    object_type='emoji',
    object_data={'emoji': '🔄', 'size': 100},
    rotation_type='clockwise',
    full_rotations=2
)

# 揺れながら回転
frames = create_spin_animation(rotation_type='wobble', full_rotations=3)

# ローディングスピナー
frames = create_loading_spinner(spinner_type='dots')
```

### パルス / ハートビート

```python
from templates.pulse import create_pulse_animation, create_attention_pulse

# スムーズなパルス
frames = create_pulse_animation(
    object_data={'emoji': '❤️', 'size': 100},
    pulse_type='smooth',
    scale_range=(0.8, 1.2)
)

# ハートビート（ダブルパンプ）
frames = create_pulse_animation(pulse_type='heartbeat')

# 絵文字GIF用のアテンションパルス
frames = create_attention_pulse(emoji='⚠️', num_frames=20)
```

### フェード

```python
from templates.fade import create_fade_animation, create_crossfade

# フェードイン
frames = create_fade_animation(fade_type='in')

# フェードアウト
frames = create_fade_animation(fade_type='out')

# 2つの絵文字間のクロスフェード
frames = create_crossfade(
    object1_data={'emoji': '😊', 'size': 100},
    object2_data={'emoji': '😂', 'size': 100}
)
```

### ズーム

```python
from templates.zoom import create_zoom_animation, create_explosion_zoom

# 劇的にズームイン
frames = create_zoom_animation(
    zoom_type='in',
    scale_range=(0.1, 2.0),
    add_motion_blur=True
)

# ズームアウト
frames = create_zoom_animation(zoom_type='out')

# 爆発ズーム
frames = create_explosion_zoom(emoji='💥')
```

### 爆発 / 砕ける

```python
from templates.explode import create_explode_animation, create_particle_burst

# バースト爆発
frames = create_explode_animation(
    explode_type='burst',
    num_pieces=25
)

# シャターエフェクト
frames = create_explode_animation(explode_type='shatter')

# パーティクルに溶解
frames = create_explode_animation(explode_type='dissolve')

# パーティクルバースト
frames = create_particle_burst(particle_count=30)
```

### ウィグル / ジグル（小刻みな揺れ）

```python
from templates.wiggle import create_wiggle_animation, create_excited_wiggle

# ゼリーのような揺れ
frames = create_wiggle_animation(
    wiggle_type='jello',
    intensity=1.0,
    cycles=2
)

# 波の動き
frames = create_wiggle_animation(wiggle_type='wave')

# 絵文字GIF用の興奮したウィグル
frames = create_excited_wiggle(emoji='🎉')
```

### スライド

```python
from templates.slide import create_slide_animation, create_multi_slide

# 左からオーバーシュート付きでスライドイン
frames = create_slide_animation(
    direction='left',
    slide_type='in',
    overshoot=True
)

# 横切るスライド
frames = create_slide_animation(direction='left', slide_type='across')

# 複数のオブジェクトが順番にスライドイン
objects = [
    {'data': {'emoji': '🎯', 'size': 60}, 'direction': 'left', 'final_pos': (120, 240)},
    {'data': {'emoji': '🎪', 'size': 60}, 'direction': 'right', 'final_pos': (240, 240)}
]
frames = create_multi_slide(objects, stagger_delay=5)
```

### フリップ（反転）

```python
from templates.flip import create_flip_animation, create_quick_flip

# 2つの絵文字間の水平フリップ
frames = create_flip_animation(
    object1_data={'emoji': '😊', 'size': 120},
    object2_data={'emoji': '😂', 'size': 120},
    flip_axis='horizontal'
)

# 垂直フリップ
frames = create_flip_animation(flip_axis='vertical')

# 絵文字GIF用のクイックフリップ
frames = create_quick_flip('👍', '👎')
```

### モーフ / トランスフォーム

```python
from templates.morph import create_morph_animation, create_reaction_morph

# クロスフェードモーフ
frames = create_morph_animation(
    object1_data={'emoji': '😊', 'size': 100},
    object2_data={'emoji': '😂', 'size': 100},
    morph_type='crossfade'
)

# スケールモーフ（一方が縮みながらもう一方が大きくなる）
frames = create_morph_animation(morph_type='scale')

# スピンモーフ（3Dフリップのような）
frames = create_morph_animation(morph_type='spin_morph')
```

### 移動エフェクト

```python
from templates.move import create_move_animation

# 直線的な移動
frames = create_move_animation(
    object_type='emoji',
    object_data={'emoji': '🚀', 'size': 60},
    start_pos=(50, 240),
    end_pos=(430, 240),
    motion_type='linear',
    easing='ease_out'
)

# 弧を描く移動（放物線軌道）
frames = create_move_animation(
    object_type='emoji',
    object_data={'emoji': '⚽', 'size': 60},
    start_pos=(50, 350),
    end_pos=(430, 350),
    motion_type='arc',
    motion_params={'arc_height': 150}
)

# 円運動
frames = create_move_animation(
    object_type='emoji',
    object_data={'emoji': '🌍', 'size': 50},
    motion_type='circle',
    motion_params={
        'center': (240, 240),
        'radius': 120,
        'angle_range': 360  # 完全な円
    }
)

# 波の動き
frames = create_move_animation(
    motion_type='wave',
    motion_params={
        'wave_amplitude': 50,
        'wave_frequency': 2
    }
)

# または低レベルのイージング関数を使用
from core.easing import interpolate, calculate_arc_motion

for i in range(num_frames):
    t = i / (num_frames - 1)
    x = interpolate(start_x, end_x, t, easing='ease_out')
    # または: x, y = calculate_arc_motion(start, end, height, t)
```

### 万華鏡エフェクト

```python
from templates.kaleidoscope import apply_kaleidoscope, create_kaleidoscope_animation

# 単一フレームに適用
kaleido_frame = apply_kaleidoscope(frame, segments=8)

# またはアニメーション万華鏡を作成
frames = create_kaleidoscope_animation(
    base_frame=my_frame,  # またはデモパターンの場合はNone
    num_frames=30,
    segments=8,
    rotation_speed=1.0
)

# シンプルなミラーエフェクト（より高速）
from templates.kaleidoscope import apply_simple_mirror

mirrored = apply_simple_mirror(frame, mode='quad')  # 4方向ミラー
# モード: 'horizontal', 'vertical', 'quad', 'radial'
```

**プリミティブを自由に組み合わせるには、以下のパターンに従ってください：**

```python
# Example: Bounce + shake for impact
for i in range(num_frames):
    frame = create_blank_frame(480, 480, bg_color)

    # Bounce motion
    t_bounce = i / (num_frames - 1)
    y = interpolate(start_y, ground_y, t_bounce, 'bounce_out')

    # Add shake on impact (when y reaches ground)
    if y >= ground_y - 5:
        shake_x = math.sin(i * 2) * 10
        x = center_x + shake_x
    else:
        x = center_x

    draw_emoji(frame, '⚽', (x, y), size=60)
    builder.add_frame(frame)
```

## ヘルパーユーティリティ

これらは一般的なニーズに対応するオプションのヘルパーです。**必要に応じてこれらを使用、変更、またはカスタム実装に置き換えてください。**

### GIF ビルダー（アセンブリと最適化）

```python
from core.gif_builder import GIFBuilder

# 選択した設定でビルダーを作成
builder = GIFBuilder(width=480, height=480, fps=20)

# フレームを追加（作成方法は問わない）
for frame in my_frames:
    builder.add_frame(frame)

# 最適化して保存
builder.save('output.gif',
             num_colors=128,
             optimize_for_emoji=False)
```

主な機能:

- 自動カラー量子化
- 重複フレーム削除
- Slack の制限に対するサイズ警告
- 絵文字モード（積極的な最適化）

### テキストレンダリング

絵文字のような小さな GIF では、テキストの可読性が課題です。一般的な解決策はアウトラインを追加することです：

```python
from core.typography import draw_text_with_outline, TYPOGRAPHY_SCALE

# アウトライン付きテキスト（可読性を向上）
draw_text_with_outline(
    frame, "BONK!",
    position=(240, 100),
    font_size=TYPOGRAPHY_SCALE['h1'],  # 60px
    text_color=(255, 68, 68),
    outline_color=(0, 0, 0),
    outline_width=4,
    centered=True
)
```

カスタムテキストレンダリングを実装するには、PIL の`ImageDraw.text()`を使用します。これは大きな GIF では問題なく機能します。

### カラーマネジメント

プロフェッショナルな見た目の GIF は、統一されたカラーパレットを使用することがよくあります：

```python
from core.color_palettes import get_palette

# 事前作成されたパレットを取得
palette = get_palette('vibrant')  # または 'pastel', 'dark', 'neon', 'professional'

bg_color = palette['background']
text_color = palette['primary']
accent_color = palette['accent']
```

色を直接扱うには、RGB タプルを使用します - ユースケースに応じて何でも使用できます。

### ビジュアルエフェクト

衝撃の瞬間のためのオプションエフェクト：

```python
from core.visual_effects import ParticleSystem, create_impact_flash, create_shockwave_rings

# パーティクルシステム
particles = ParticleSystem()
particles.emit_sparkles(x=240, y=200, count=15)
particles.emit_confetti(x=240, y=200, count=20)

# 各フレームで更新とレンダリング
particles.update()
particles.render(frame)

# フラッシュエフェクト
frame = create_impact_flash(frame, position=(240, 200), radius=100)

# 衝撃波リング
frame = create_shockwave_rings(frame, position=(240, 200), radii=[30, 60, 90])
```

### イージング関数

スムーズな動きには線形補間ではなくイージングを使用します：

```python
from core.easing import interpolate

# 落下するオブジェクト（加速）
y = interpolate(start=0, end=400, t=progress, easing='ease_in')

# 着地するオブジェクト（減速）
y = interpolate(start=0, end=400, t=progress, easing='ease_out')

# バウンス
y = interpolate(start=0, end=400, t=progress, easing='bounce_out')

# オーバーシュート（弾性）
scale = interpolate(start=0.5, end=1.0, t=progress, easing='elastic_out')
```

使用可能なイージング: `linear`, `ease_in`, `ease_out`, `ease_in_out`, `bounce_out`, `elastic_out`, `back_out`（オーバーシュート）、その他は`core/easing.py`にあります。

### フレーム合成

必要に応じて使用できる基本的な描画ユーティリティ：

```python
from core.frame_composer import (
    create_gradient_background,  # グラデーション背景
    draw_emoji_enhanced,         # オプションで影付き絵文字
    draw_circle_with_shadow,     # 奥行きのある図形
    draw_star                    # 5つ星
)

# グラデーション背景
frame = create_gradient_background(480, 480, top_color, bottom_color)

# 影付き絵文字
draw_emoji_enhanced(frame, '🎉', position=(200, 200), size=80, shadow=True)
```

## 最適化戦略

GIF が大きすぎる場合：

**メッセージ GIF（>2MB）の場合：**

1. フレーム数を減らす（FPS を下げるか長さを短くする）
2. 色数を減らす（128 → 64 色）
3. 寸法を減らす（480x480 → 320x320）
4. 重複フレーム削除を有効化

**絵文字 GIF（>64KB）の場合 - 積極的に：**

1. 合計 10-12 フレームに制限
2. 最大 32-40 色を使用
3. グラデーションを避ける（単色の方が圧縮率が良い）
4. デザインを簡素化（要素を減らす）
5. save メソッドで`optimize_for_emoji=True`を使用

## 組み合わせパターンの例

### シンプルなリアクション（パルス）

```python
builder = GIFBuilder(128, 128, 10)

for i in range(12):
    frame = Image.new('RGB', (128, 128), (240, 248, 255))

    # Pulsing scale
    scale = 1.0 + math.sin(i * 0.5) * 0.15
    size = int(60 * scale)

    draw_emoji_enhanced(frame, '😱', position=(64-size//2, 64-size//2),
                       size=size, shadow=False)
    builder.add_frame(frame)

builder.save('reaction.gif', num_colors=40, optimize_for_emoji=True)

# 検証
from core.validators import check_slack_size
check_slack_size('reaction.gif', is_emoji=True)
```

### 衝撃を伴うアクション（バウンス + フラッシュ）

```python
builder = GIFBuilder(480, 480, 20)

# フェーズ1: オブジェクトが落下
for i in range(15):
    frame = create_gradient_background(480, 480, (240, 248, 255), (200, 230, 255))
    t = i / 14
    y = interpolate(0, 350, t, 'ease_in')
    draw_emoji_enhanced(frame, '⚽', position=(220, int(y)), size=80)
    builder.add_frame(frame)

# フェーズ2: 衝撃 + フラッシュ
for i in range(8):
    frame = create_gradient_background(480, 480, (240, 248, 255), (200, 230, 255))

    # 最初のフレームでフラッシュ
    if i < 3:
        frame = create_impact_flash(frame, (240, 350), radius=120, intensity=0.6)

    draw_emoji_enhanced(frame, '⚽', position=(220, 350), size=80)

    # テキストが表示される
    if i > 2:
        draw_text_with_outline(frame, "GOAL!", position=(240, 150),
                              font_size=60, text_color=(255, 68, 68),
                              outline_color=(0, 0, 0), outline_width=4, centered=True)

    builder.add_frame(frame)

builder.save('goal.gif', num_colors=128)
```

### プリミティブの組み合わせ（移動 + シェイク）

```python
from templates.shake import create_shake_animation

# シェイクアニメーションを作成
shake_frames = create_shake_animation(
    object_type='emoji',
    object_data={'emoji': '😰', 'size': 70},
    num_frames=20,
    shake_intensity=12
)

# シェイクをトリガーする移動要素を作成
builder = GIFBuilder(480, 480, 20)
for i in range(40):
    t = i / 39

    if i < 20:
        # トリガー前 - 移動するオブジェクトを含む空のフレームを使用
        frame = create_blank_frame(480, 480, (255, 255, 255))
        x = interpolate(50, 300, t * 2, 'linear')
        draw_emoji_enhanced(frame, '🚗', position=(int(x), 300), size=60)
        draw_emoji_enhanced(frame, '😰', position=(350, 200), size=70)
    else:
        # トリガー後 - シェイクフレームを使用
        frame = shake_frames[i - 20]
        # 最終位置に車を追加
        draw_emoji_enhanced(frame, '🚗', position=(300, 300), size=60)

    builder.add_frame(frame)

builder.save('scare.gif')
```

## 哲学

このツールキットはビルディングブロックを提供し、硬直したレシピではありません。GIF リクエストに取り組むには：

1. **クリエイティブビジョンを理解する** - 何が起こるべきか？雰囲気は？
2. **アニメーションを設計する** - フェーズに分割（予備動作、アクション、リアクション）
3. **必要に応じてプリミティブを適用する** - シェイク、バウンス、移動、エフェクト - 自由に組み合わせ
4. **制約を検証する** - ファイルサイズをチェック、特に絵文字 GIF の場合
5. **必要に応じて反復する** - サイズ制限を超える場合はフレーム/色を減らす

**目標は Slack の技術的制約内でのクリエイティブの自由です。**

## 依存関係

このツールキットを使用するには、まだインストールされていない場合のみ以下の依存関係をインストールしてください：

```bash
pip install pillow imageio numpy
```
