# Codex の Enter キーを改行にする（Mac）

日本語 | [English](README.en.md)

macOS の Codex デスクトップアプリで、Karabiner-Elements を使ってメッセージ入力時のキー操作を変更する設定です。

| 操作 | 変更後の動作 |
| --- | --- |
| Enter | 改行（Shift + Enter を送る） |
| Command + Enter | 送信（Enter を送る） |

この設定は、Codex が前面にある間にキー入力を変換します。**Codex のプラグインや公式機能ではありません。** macOS の外部ツールで実現しています。Windows は対象外です。

## はじめに確認してください

- ルールは Codex のアプリ全体を対象にします。メッセージ入力欄以外でも Enter の動作が変わる場合があります。
- 日本語の漢字変換中の Enter にも影響する可能性があります。変換の確定や候補選択を必ず試し、問題があれば下記の方法で無効にしてください。
- 作成者の Mac の Codex デスクトップアプリで改行と送信ができた設定ですが、すべての環境や日本語入力方式での動作を保証するものではありません。
- 他の Karabiner-Elements のルールや、Codex の更新によって動作が変わる場合があります。

## 設定手順

### 1. Karabiner-Elements をインストール

1. [Karabiner-Elements の公式サイト](https://karabiner-elements.pqrs.org/)からダウンロードし、インストールします。
2. Karabiner-Elements を起動します。macOS に権限の設定を求められた場合は、画面の案内に従って設定してください。キー入力を変更するための権限が必要です。詳細は[公式の macOS 設定手順](https://karabiner-elements.pqrs.org/docs/manual/misc/required-macos-settings/)を参照してください。

### 2. Codex 用のルールを追加

1. Karabiner-Elements の設定画面で **Complex Modifications** を開きます。
2. **Add your own rule** を選びます。表示名はバージョンにより異なる場合があります。
3. エディタに表示されるサンプルをすべて削除します。
4. 下の JSON をすべてコピーして貼り付け、**Save** を押します。

```json
{
  "description": "Codex: Enter for newline, Command+Enter to send",
  "manipulators": [
    {
      "type": "basic",
      "from": {
        "key_code": "return_or_enter",
        "modifiers": {
          "mandatory": ["command"],
          "optional": ["caps_lock"]
        }
      },
      "to": [{ "key_code": "return_or_enter" }],
      "conditions": [
        {
          "type": "frontmost_application_if",
          "bundle_identifiers": ["^com\\.openai\\.codex$"]
        }
      ]
    },
    {
      "type": "basic",
      "from": {
        "key_code": "return_or_enter",
        "modifiers": {
          "optional": ["caps_lock"]
        }
      },
      "to": [
        {
          "key_code": "return_or_enter",
          "modifiers": ["left_shift"]
        }
      ],
      "conditions": [
        {
          "type": "frontmost_application_if",
          "bundle_identifiers": ["^com\\.openai\\.codex$"]
        }
      ]
    }
  ]
}
```

同じルールは [codex-enter-rule.json](codex-enter-rule.json) にも置いています。[Karabiner-Elements の公式手順](https://karabiner-elements.pqrs.org/docs/manual/configuration/add-your-own-complex-modifications/)も参照できます。

### 3. 動作を確認

Codex に戻り、テスト用の短い文章で次を順に確認します。

1. 文字を入力し、**Enter** で送信せずに改行できること。
2. **Command + Enter** でメッセージを送信できること。
3. 日本語を入力し、変換候補の選択や Enter による確定が普段どおりできること。
4. 検索欄やメニューなど、Codex 内で普段使うほかの場所の Enter が支障なく使えること。
5. 他のアプリで Enter が通常どおり動くこと。

想定どおり動かない場合は、無理に使わずルールを削除してください。日本語入力の問題は入力方式によって異なる可能性があります。

## 無効化・削除する

Karabiner-Elements を開き、**Complex Modifications** の一覧から `Codex: Enter for newline, Command+Enter to send` を探して削除します。これで、このルールによるキー変換は止まります。

## うまく動かないとき

- **変化がない**：ルールが保存されているか、Karabiner-Elements が起動しているか、macOS の必要な権限が許可されているか確認します。
- **Codex 以外で動く／Codex で動かない**：対象は `com.openai.codex` というアプリ識別子です。お使いのアプリと一致するか、[Karabiner-EventViewer の Frontmost Application](https://karabiner-elements.pqrs.org/docs/json/complex-modifications-manipulator-definition/conditions/frontmost-application/) で確認してください。
- **メニュー操作や日本語変換に支障がある**：ルールを削除してください。現状、入力欄や変換中かどうかの判定は行っていません。
- **予期しないキー動作になる**：ほかの Complex Modifications との競合や順序を確認してください。

## 仕組み

Karabiner-Elements が、前面アプリの識別子が `com.openai.codex` のときにキー入力を変換します。Enter は Shift + Enter に、Command + Enter は修飾キーなしの Enter に変換します。Codex がこれらをそれぞれ改行・送信として処理することを利用しています。その他のアプリにはこのルールは適用されません。
