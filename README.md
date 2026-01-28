> ⚠️ **Fork notice**
>
> This repository is a fork of the original GJS OSK project by **Vishram1123**:  
> https://github.com/Vishram1123/gjs-osk
>
> This fork is maintained for **open.Yellow.os** and includes additional
> custom keyboard layouts and minor adjustments for Japanese environments.
>  
> The original design, architecture, and license (GPL-3.0) are fully respected.

# [GJS OSK](https://extensions.gnome.org/extension/5949/gjs-osk/)
A (marginally) better on screen keyboard for GNOME 45+ (go to the [pre-45 branch](https://github.com/Vishram1123/gjs-osk/tree/pre-45) for compatibility from gnome 42-44)
## Advantages over the default OSK:
-	Function, modifier, tab, and arrow key support
-	Ability to move around the screen
-	More compact layout
## Requirements
- GNOME 45 or above
- Wayland (X11 is not working properly)
## Demo
[Keyboard Demo.webm](https://user-images.githubusercontent.com/64966832/210458851-1b91adba-f6e4-4d40-b0d5-dba2c46cc354.webm)

[Settings Demo.webm](https://user-images.githubusercontent.com/64966832/210458854-eb458311-3d3f-4edb-93df-f5b8334d4cbc.webm)

## Install
1. Visit [https://extensions.gnome.org/extension/5949/gjs-osk/](https://extensions.gnome.org/extension/5949/gjs-osk/)
2. Confirming that you have Chrome GNOME shell installed on your computer and your browser's GNOME Shell Integration plugin
3. Click Install, and accept the prompt
## Install from Source
1. [Head to the releases in the sidebar](https://github.com/Vishram1123/gjs-osk/releases/latest)
2. Download `gjsosk@vishram1123_[version].zip` (`main` for GNOME version >= 45, `pre-45` for GNOME verison <= 44)
3. Run `gnome-extensions install /path/to/gjsosk@vishram1123_[version].zip` (replace with appropriate path)
4. Log out of GNOME and log back in. 
5. Click on the keyboard button in the dash bar
## Usage
- To drag the keyboard around, click on the move icon in the bottom right, then drag the keyboard around the screen. To get the full keyboard back, press the move icon again.
  - The keyboard will snap to the corners, edges, and center of the screen.
- To change properties about the keyboard, open up the "Extensions" application, and click on "Settings" under this extension to get a list of changeable properties
  - Close the settings dialog to save any modified settings
- To type special characters, open GNOME settings, and turn on "Compose Key" under the Keyboard submenu. Choose a modifier (preferably right alt), and use the [key combinations listed here](https://en.wikipedia.org/wiki/Compose_key#Common_compose_combinations) to type special characters
- To change the keyboard layout, change the layout in Gnome's Control Center
- To add typing prediction, add "Typing Booster" as an input source (in GNOME's settings), and keep it chosen as the primary input source [(extended guide here)](https://mike-fabian.github.io/ibus-typing-booster/docs/user/).
  - Note that this will cause predictive text to be present even without the OSK open, and the input language for Typing Booster's predictions will have to be set in Typing Booster's settings 
- To open the keyboard from the command line (or with a shortcut), run the command `dconf write /org/gnome/shell/extensions/gjsosk/indicator/opened true` which will open the keyboard 
## Known Problems/Issues and Intended Features (Would appreciate solutions about how to fix):
- 100% width or height doesn't take up the full monitor width or height (minus 25 px on either side). Instead, it is 1 or 2 px smaller, depending on the monitor size
## Help
- If you find any bugs, or if you have any suggestions, please open an issue or submit a pull request. Thanks!
### Keyboard Layouts
- As of recently, all keyboard layouts and variants (available through localectl) have been added to GJS-OSK. Please report on the state of keyboard layouts as correct/incorrect in issue [#48](https://github.com/Vishram1123/gjs-osk/issues/48), and I will try to fix them promptly.
  - To generate a single keyboard layout, install `xkbcommon` through `pip` and run `genKeyMap.py` with `layout+variant` as the argument (`pip install xkbcommon` then `python genKeyMap.py de+dvorak` for example)

**Help in this area is greatly appreciated!**

## open.Yellow.os customizations

This fork adds the following features on top of the upstream GJS OSK:

- Custom **US** and **JP** physical keyboard layouts for open.Yellow.os
- Japanese-specific key support (e.g. Hankaku/Zenkaku via KEY_GRAVE)
- Layout adjustments tested with **Wayland + Fcitx5**
- Minor fixes to ensure correct key labels in Japanese keyboard environments

These changes are intended to improve the out-of-box experience on
open.Yellow.os while keeping upstream behavior intact.

## CI/CD の仕組み（開発者向け）

`gjs-osk` の開発では GitHub Actions を利用した CI/CD を導入しています。  
修正内容を push → tag を付与 → GitHub Actions で自動ビルド → `apt-repo-infra` でリポジトリ公開、という流れです。 

```mermaid
flowchart LR
    dev["開発者 (tag vX.Y.Z)"] --> actions["GitHub Actions (gjs-osk)"]
    actions --> release["GitHub Release（成果物添付）"]
    release -->|Run workflow 手動| infra["apt-repo-infra（Run workflow 実行）"]
    infra --> repo["deb.openyellowos.org（APT リポジトリに公開）"]
``` 

### フロー概要

1. **ソースコード修正**
   ```bash
   git clone https://github.com/openyellowos/gjs-osk.git
   cd gjs-osk
   ```

2. **プログラム修正**
   - `gjsosk@openyellowos.com` を編集する。  
   - 必要があれば README.md も修正する。  

3. **changelog 更新**
   ```bash
   debchange -i
   ```
   - changelog に修正内容を記入する。

   例:
   ```text
   gjs-osk (1.1-1) kerria; urgency=medium

     * 設定変更の不具合を修正
     * README.md の利用方法を更新

    -- 開発者名 <you@example.com>  Sat, 31 Aug 2025 20:00:00 +0900
   ```

4. **コミット & push**
   ```bash
   git add .
   git commit -m "修正内容を記述"
   git push origin main
   ```

5. **タグ付与**
   ```bash
   git tag v0.1.0
   git push origin v0.1.0
   ```

6. **GitHub Actions による自動ビルド**
   - タグ push を検知してワークフローが起動。  
   - `.deb` がビルドされ、GitHub Release に添付される。  

7. **APT リポジトリ公開**
   - `apt-repo-infra` の GitHub Actions を **手動で Run workflow** する。  
   - 実際の入力例：  
     - Target environment: `production`  

   - 実行すると apt リポジトリに反映される。  
   - 利用者は以下で最新を取得可能：  
     ```bash
     sudo apt update
     sudo apt install gjs-osk
     ```

---

## 開発環境に必要なパッケージ & ローカルでビルドする手順

### 必要なツールのインストール
```bash
sudo apt update
sudo apt install -y devscripts build-essential debhelper lintian
```

### deb-src を有効にする
1. `/etc/apt/sources.list` を編集します。
   ```bash
   sudo nano /etc/apt/sources.list
   ```
2. 以下のような行を探し、コメントアウトを解除してください。
   ```text
   deb http://deb.debian.org/debian trixie main contrib non-free-firmware
   # deb-src http://deb.debian.org/debian trixie main contrib non-free-firmware
   ```
   ↓ 変更後
   ```text
   deb-src http://deb.debian.org/debian trixie main contrib non-free-firmware
   ```
3. 保存して終了後、更新します。
   ```bash
   sudo apt update
   ```

### ビルド依存の導入
```bash
sudo apt-get build-dep -y ./
```

### ローカルビルド
```bash
# 署名なしでバイナリのみビルド
dpkg-buildpackage -us -uc -b
# または（同等）
debuild -us -uc -b
```
- 生成物: `../gjs-osk_*_amd64.deb`（親ディレクトリに出力）  

### テストインストール / アンインストール
```bash
sudo apt install ./../gjs-osk_*_amd64.deb
# 動作確認後に削除する場合
sudo apt remove gjs-osk
```

### クリーン
```bash
# パッケージの生成物を削除
fakeroot debian/rules clean
# もしくは
dpkg-buildpackage -T clean
```

---

### 注意事項

- **必ず changelog を更新すること**  
- **バージョン番号は changelog, git tag, GitHub Release を揃えること**  
- **依存関係変更時は debian/control を更新すること**  

