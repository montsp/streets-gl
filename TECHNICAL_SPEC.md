# フライトシミュレーター 技術仕様書 (Technical Specification)

## 1. 物理エンジン・航空力学 (FDM)
### JSBSim WASM Integration
- **理由**: JSBSimは航空宇宙レベルの精度を持ち、C++で書かれているためWASMへのコンパイルに最適。
- **統合方法**:
    - `Emscripten` を使用して JSBSim をコンパイル。
    - メインスレッドの負荷を避けるため、`Web Worker` 内で物理計算を実行。
    - 入力（スロットル、操舵）を Worker に送り、結果（座標、姿勢、速度）をメインスレッドに返す。
- **機体モデル**:
    - 初期段階では `Cessna 172` (プロペラ機) と `Boeing 737` (ジェット旅客機) のXML定義をプリセットとして用意。

## 2. 視覚効果 (Visual Effects)
### 空港の動的生成
- **ランウェイ・ライティング**:
    - `aeroway=runway` を含むOSMウェイを抽出し、頂点シェーダーでライトを配置。
    - 夜間や悪天候時に発光するマテリアルを動的に適用。
- **PAPI (進入角指示灯)**:
    - 進入角度に基づき、赤・白が切り替わるカスタムシェーダー。

### 雲と雰囲気
- **Volumetric Clouds**: Streets GLの既存の雰囲気レンダリングを拡張し、レイマーチングを使用した体積雲を実装。
- **Aerial Perspective**: 距離に応じた青みがかりや霧を動的に調整。

## 3. 入力システム (Input Abstraction)
### Input Mapper
- **Keyboard**: `W/S` (Pitch), `A/D` (Roll), `Q/E` (Yaw), `Shift/Ctrl` (Throttle).
- **Gamepad**: 左スティック（スロットル/ヨー）、右スティック（ピッチ/ロール）。
- **Touch**: 画面左右に配置されたバーチャルジョイスティック。

## 4. データパイプライン
### API統合
- **METAR API**:
    - ユーザーの最寄り、または目的地空港のMETARを `checkwx.com` 等から定期的に取得。
    - JSBSimの風向、風速、気圧、温度プロパティに反映。
- **OurAirports Data**:
    - 空港名、ICAOコード、滑走路データをキャッシュし、検索機能を提供。

## 5. UI/UX (HUD)
### Canvas 2D + WebGL Overlay
- 高速な更新が必要な計器（水平儀、速度計）は、メインの3Dキャンバスとは別のオーバーレイレイヤーで描画。
- **HUD (Head-Up Display)**:
    - F-16や最新の旅客機スタイルのHUD。
    - ターゲットマーカー（目的地への方向）を表示。
