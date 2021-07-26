<!-- omit in toc -->
# Lambda用IAMロール作成

Lambda関数に付与するIAMロールを作成します。

## 1. 変数の指定

### 1.1. ロール名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    IAM_ROLE_NAME='internship-lambda-role'-${ORIGINNAME}

### 1.2. ポリシーのパス

    IAM_ROLE_PATH='/internship-cli/'

### 1.3. ポリシーの説明

    IAM_ROLE_DESCRIPTION='Role for lambda.'

### 1.4. ドキュメント保存ディレクトリ

    DIR_IAM_ROLE_DOC="${HOME}/environment/conf-lambda"
    IAM_ROLE_DOC_NAME='internship-lambda-role'
    FILE_IAM_ROLE_DOC="${DIR_IAM_ROLE_DOC}/${IAM_ROLE_DOC_NAME}.json" \
        && echo ${FILE_IAM_ROLE_DOC}

## 2. メインの処理

### 2.1. ロール作成

    aws iam create-role \
        --role-name ${IAM_ROLE_NAME} \
        --path "${IAM_ROLE_PATH}" \
        --assume-role-policy-document file://${FILE_IAM_ROLE_DOC} \
        --description "${IAM_ROLE_DESCRIPTION}"

## 3. 確認

### 3.1. ロールの存在確認

ロール名が表示されたらOK

    aws iam list-roles \
        --query "Roles[?RoleName == \`${IAM_ROLE_NAME}\`].RoleName" \
        --output text
