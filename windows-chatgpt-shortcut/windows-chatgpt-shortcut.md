# windowsで、選択範囲をChatgptに即送る方法

開発を行っているときやブラウザで何かを読んでいるとき、ChatGPTにコピペして質問することがよくあります。

その手間が面倒なので、選択範囲をすぐGPTに送れるようにしてみました。

前提としてChatGPTの公式アプリのインストールが必要です。

## 導入手順（3 分で終わります）

以下をコピー

```autohotkey
#Requires AutoHotkey v2.0
; ────────────────────────────────────────────────
;  Hotkey:  Ctrl + Shift + q
;  Step 1  : コピー
;  Step 2  : ChatGPT デスクトップ アプリのクイックバー呼び出し
;  Step 3  : ペーストして送信
; ────────────────────────────────────────────────
^+q:: {
    ClipSaved := ClipboardAll()        ; 既存クリップボードを退避
    Send "^c"                          ; 選択テキストをコピー
    ClipWait 0.5                       ; コピー完了待ち

    Send "^+g"                    ; ChatGPT アプリのオーバーレイを開く
    Sleep 500                          ; ウインドウが出るまで 0.5 秒ほど待機
    Send "^v"                   ; 貼り付け

    Clipboard := ClipSaved             ; クリップボードを元に戻す
}
return
```

1. **AutoHotkey v2 をインストールして起動**
   https://www.autohotkey.com → *Download* → *v2 Installer* を選択。インストール後起動。

2. 上記コードを `send_to_chatgpt.ahk` などのファイルに保存。

3. ダブルクリックして常駐。
   - 自動起動したい場合は `shell:startup` フォルダにショートカットを置く。

4. **ChatGPT アプリのショートカット変更(デフォルトだと他アプリのショートカットとぶつかるため)**
   ChatGPT → *Settings → App → Hot Key*でShift+ctrl+gに変更する。

これでどこにいてもShift+ctrl+qを押せばすぐChatGPTに送れます。

貼付けされないときはsleepを1000くらいにしてみてください。
