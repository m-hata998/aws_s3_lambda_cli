<!-- omit in toc -->
# Lambda用IAMロールの信頼ポリシードキュメント作成

Lambda関数に付与するIAMロール用の信頼ポリシードキュメントを作成します。

## 1. 変数の指定

### 1.1. プリンシパル

    IAM_ROLE_PRINCIPAL='lambda.amazonaws.com'

### 1.2. ドキュメント保存ディレクトリ

    DIR_IAM_ROLE_DOC="${HOME}/environment/conf-lambda"

#### 1.2.1. ディレクトリの存在確認

    ls -d ${DIR_IAM_ROLE_DOC}

ディレクトリが存在しない（[ls: cannot access XXXXX : No such file or directory]と表示される場合  
下記を実行しディレクトリ作成

    mkdir -p ${DIR_IAM_ROLE_DOC}

### 1.3. ドキュメントファイル名

    IAM_ROLE_DOC_NAME='internship-lambda-role'
    FILE_IAM_ROLE_DOC="${DIR_IAM_ROLE_DOC}/${IAM_ROLE_DOC_NAME}.json" \
        && echo ${FILE_IAM_ROLE_DOC}

## 2. メインの処理

### 2.1. 信頼ポリシードキュメント作成

    cat << EOF > ${FILE_IAM_ROLE_DOC}
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Action": "sts:AssumeRole",
                "Principal": {
                    "Service": "${IAM_ROLE_PRINCIPAL}"
                },
                "Effect": "Allow",
                "Sid": ""
            }
        ]
    }
    EOF

    cat ${FILE_IAM_ROLE_DOC}

### 2.2. JSONフォーマットの確認

エラーが出力されなければOK

    cat ${FILE_IAM_ROLE_DOC} \
        |  python3 -m json.tool \
        > /dev/null

## 3. 確認

### 3.1. ファイルの存在確認

ファイルのパスが表示されたらOK

    ls ${FILE_IAM_ROLE_DOC}
