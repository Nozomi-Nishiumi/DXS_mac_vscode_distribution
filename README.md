# 【重要】Mac VS Code C++ 開発環境構築ガイド

VRプログラミング実習のための開発環境を、各自の Mac に構築します。下記の手順を上から順に実施してください。各ツールが何のためのものかも併記しています。

## やることリスト
- **手動インストール**：Homebrew / VS Code / Blender
- **Homebrew 経由でインストール**：OpenCV / XQuartz
- **VS Code 拡張機能**：C/C++ Extension Pack / CodeLLDB
- **動作確認**：配布プロジェクトをクローンして実行

> **注意：プロジェクトはクラウド同期フォルダ（iCloud / Google Drive / OneDrive 等）に置かないでください。** 同期がファイルを古い版に巻き戻し、編集が消える事故が起きます。`~/Developer/` などローカルに置いてください。

---

## 手順1. Homebrew（パッケージ管理ツール ＆ C++コンパイラ）
Homebrew はインストール支援ツールで、これを用いるとインストール作業の大部分を自動化できます。

1. 「ターミナル」アプリを開きます（Spotlight 検索で "ターミナル"）。ターミナルはテキストでパソコンとやりとりするアプリで、Mac に標準搭載されています。
2. 以下のインストールコマンドを貼り付けて実行します。
   ```
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
3. 途中で「コマンドラインデベロッパツール（Command Line Tools）をインストールしますか」と表示されたら、**「インストール」を選んで承認**してください（無料・Apple ID 不要）。これで C++ コンパイラ（clang）が入り、VS Code でのビルドに使われます。
4. インストール後に表示される **"Next steps"** の2行（`echo ... >> ~/.zprofile` と `eval ...`）をそのまま実行して PATH を通します。
5. 確認：新しいターミナルで `brew -v` を実行し、`Homebrew x.x.x` と表示されれば成功です。
   - `brew --prefix` が **`/opt/homebrew`**（Apple Silicon 標準）であることも確認してください。

## 手順2. OpenCV（画像処理ライブラリ）
画像処理を行うためのライブラリです。ターミナルで:
```
brew install opencv
```
ビルド済みパッケージのダウンロードが中心で、比較的すぐ終わります。

## 手順3. XQuartz（描画補助）
OpenGL 描画に使う X11 環境です。ターミナルで:
```
brew install --cask xquartz
```
既に `/opt/X11` がある場合は不要です。

## 手順4. Blender（CG作成ツール）
仮想空間やモデルの確認に使うコンピュータグラフィックス作成ツールです。公式サイトからダウンロードしてインストールしてください。
- https://www.blender.org/download/

## 手順5. VS Code（統合開発環境）
講義で使用する IDE です。公式サイトからダウンロードします。
- https://code.visualstudio.com/
- ダウンロードした dmg をダブルクリックし、アプリを「アプリケーション」フォルダに移動すればインストール完了です。

## 手順6. プロジェクトの取得（GitHub から）
配布リポジトリを**ローカルディスク**にクローンします（クラウド同期フォルダ不可）。
```
mkdir -p ~/Developer && cd ~/Developer
git clone https://github.com/Nozomi-Nishiumi/DXS_mac_vscode_distribution.git
```
VS Code の **File → Open Folder** で、クローンしたフォルダ（例 `~/Developer/DXS_mac_vscode_distribution`）を開きます。フォルダの作成者を信頼するか聞かれたら **Yes** をクリックします。

## 手順7. C++ 開発環境用の拡張機能
画面左の **Extensions**（四角が4つ並んだアイコン）をクリックし、以下をインストールします。
- **C/C++ Extension Pack**（補完・IntelliSense・ビルド連携）
- **CodeLLDB**（`vadimcn.vscode-lldb`。Apple Silicon でのデバッグに使用）

## 手順8. 設定（ほぼ自動）
- インクルードパス等は本リポジトリ同梱の `.vscode/c_cpp_properties.json` で設定済みです（C standard: c17 / C++ standard: c++17）。
- 赤波線（IntelliSense の誤検知）が出る場合は、⌘⇧P →「**C/C++: Reset IntelliSense Database**」を実行、または VS Code を再起動してください。ビルド・実行には影響しません。

## 手順9. 動作確認
1. **⌘⇧B（Run Build Task）** を押します。既定タスク「Run OpenGL project」がビルド→実行まで自動で行います。
2. ターミナル（デバッグコンソール）に **`Select mode (1–7):`** と表示されれば**環境構築成功**です。番号を入力すると各モードが実行されます。

## 手順10. カメラの許可
カメラを使うモード（3〜7）では、**システム設定 → プライバシーとセキュリティ → カメラ** で **VS Code を許可**してください。

---

## デバッグについて（CodeLLDB）
「実行とデバッグ」から **Debug OpenGLApp (CodeLLDB)** を起動します。

> **既知の注意（mode 7）**：mode 7 をデバッグするとカメラ起動時に停止することがあります。これは macOS のカメラ（AVFoundation）が内部で投げて自分で処理している**無害な C++ 例外**にデバッガが反応するためで、クラッシュではありません。
> **対処**：「実行とデバッグ」ビュー左下の **BREAKPOINTS パネルで「C++: on throw」のチェックを外す**と最後まで進みます。

## モードと教材の流れ
| # | 内容 |
|---|---|
| 1 | OpenGL の基礎 |
| 2 | 静止画の色トラッキング |
| 3 | カメラ接続確認 |
| 4 | カメラのライブトラッキング基礎 |
| 5 | 色しきい値をトラックバーで調整（`output/cam0_*.yml` に自動保存） |
| 6 | カメラ較正 → `output/cam_intrinsic_prameters_test.yml` を出力 |
| 7 | PnP による VR 表示（mode 5・6 の結果を読み込み。`v` キーで視点切替） |

3Dモデル（.obj/.mtl）は `common_data/CG_objects/` に、実行時生成ファイルは `DX_studies_2025/output/` に置かれます。

## トラブルシューティング
- **`opencv2/opencv.hpp` が見つからない / リンクエラー** → 手順1・2を確認（`brew --prefix` が `/opt/homebrew` であること）。
- **`GL/glut.h` が見つからない** → 手順3の XQuartz が未導入。
- **`dyld: libOpenEXR-*.dylib not loaded` 等のバージョン不整合** → `brew update && brew upgrade opencv`（必要なら `brew link --overwrite qt`）。
- **ビルド成果物に書き込めない** → 管理者所有フォルダではなく自分のホーム配下にクローンして実行。
- **編集が勝手に元に戻る** → クラウド同期フォルダに置いている可能性。ローカルへ移動。
- **デバッグがカメラ起動で止まる** → 上記「BREAKPOINTS の C++: on throw を外す」。

> うまくいかない場合の簡易テスト用に、環境テストプロジェクトも用意しています。
> `git clone https://github.com/Nozomi-Nishiumi/DX_test_program_vscode_distribution.git`
