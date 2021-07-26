<!-- omit in toc -->
# 一時ファイルの削除

GitからCloneしたプロジェクトのディレクトリを削除します。

## 1. 変数の指定

### 1.1. ドキュメント保存ディレクトリ(Lambda)

    RESIZE_SHELL='/home/ec2-user/environment/resize.sh'

## 2. メインの処理

### 2.1. 削除(Lambda)

    rm -Rf ${RESIZE_SHELL}

## 3. 確認

### 3.1. ディレクトリの存在確認(Lambda)

[ls: cannot access /home/ec2-user/environment/resize.sh: No such file or directory]の出力があればOK

    ls ${RESIZE_SHELL}
