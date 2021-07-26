<!-- omit in toc -->
# 一時ファイルの削除

一時的に作成したポリシードキュメント・ディレクトリを削除します。

## 1. 変数の指定

### 1.1. ドキュメント保存ディレクトリ(Lambda)

    DIR_IAM_ROLE_DOC="${HOME}/environment/conf-lambda"

### ドキュメント保存ディレクトリ(Athena)

    DIR_ATHENA_DOC="${HOME}/environment/conf-athena"

## 2. メインの処理

### 2.1. 削除(Lambda)

    rm -Rf ${DIR_IAM_ROLE_DOC}

### 2.2. 削除(Athena)

    rm -Rf ${DIR_ATHENA_DOC}

## 3. 確認

### 3.1. ディレクトリの存在確認(Lambda)

[ls: cannot access /home/ec2-user/environment/conf-lambda: No such file or directory]の出力があればOK

    ls ${DIR_IAM_ROLE_DOC}

### 3.2. ディレクトリの存在確認(Athena)

[ls: cannot access /home/ec2-user/environment/conf-athena: No such file or directory]の出力があればOK

    ls ${DIR_ATHENA_DOC}
