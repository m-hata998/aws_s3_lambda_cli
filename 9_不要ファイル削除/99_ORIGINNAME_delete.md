<!-- omit in toc -->
# ORIGINNAMEファイルの削除

動作確認済

## 1. 変数の指定

### 1.1. ファイルパス

    ORIGINFILEPATH='/home/ec2-user/environment/originname'

## 2. メインの処理

### 2.1. ファイル削除

    rm ${ORIGINFILEPATH}

## 3. 確認

### 3.1. ファイルの存在確認

[ls: cannot access /home/ec2-user/environment/originname: No such file or directory]の出力があればOK

    ls ${ORIGINFILEPATH}
