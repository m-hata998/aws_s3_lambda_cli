<!-- omit in toc -->
# インターン環境構築手順

Cloud9環境にて操作する前提です。  
手順書1枚につき1ターミナルで作業する前提です。  
(エラーが発生したらその手順書のみやり直してもらえればおおよそOK)

## 事前手順

### Cloud9環境作成

- マネジメントコンソールからCloud9を検索・選択
- [CreateEnviroment]を押下（右上のリージョンが、今回の利用対象になっていることを確認）
- Nameに任意の名前を入力して[Next]を押下
- Instance type：Other instance typeから[t3.medium]を選択（今回はdockerでメモリを使用するので少し大きめ）
- Network Settingを、パブリックのネットワークが存在するVPCに変更（defaultでもOKですがAZによってはインスタンスの作成時にエラーになるかも）

### Cloud9環境設定

※ここをCloudshellのCLI手順にしたい

- Cloud9のロール・ポリシーの作成　※policyフォルダ内のJSONをそのまま設定すると最小の権限のポリシーを作成可
- マネジメントコンソールからEC2を検索・選択
- Cloud9のインスタンスを選択し、ロールをアタッチ

### Cloud9内設定

- 右上の歯車のボタンを押してSetting画面を開く
- Preferencesの画面の左メニューからAWS Settingsを選択
- CredentialsのAWS managed temporary credentialsをOFF（赤色）に変更

### CLI実行

- 0_作成フォルダの手順を順番通りに実行すればOK
- 1_削除フォルダの手順はリソース削除用手順です
- 9_不要ファイル削除フォルダの手順はCloud9を継続して利用する場合に今回作成したリソースを削除するための手順です。Cloud9ごと削除する場合は実行不要

## その他

- ポリシーのJSONについて、Resource設定の制限がないので、本番運用する場合は考慮要。
