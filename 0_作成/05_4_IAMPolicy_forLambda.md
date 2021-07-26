<!-- omit in toc -->
# Lambda用IAMロールに付与するポリシーの作成

ポリシーを作成します。

## 1. 変数の指定

### 1.1. ポリシー名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    IAM_POLICY_NAME='internship-lambda-policy'-${ORIGINNAME}

### 1.2. ポリシーパス

    IAM_POLICY_PATH='/internship-cli/'

### 1.3. ポリシーの説明

    IAM_POLICY_DESCRIPTION='Policy for internship-lambda-policy.'

### 1.4. ドキュメント名

    DIR_IAM_POLICY_DOC="${HOME}/environment/conf-lambda"
    IAM_POLICY_DOC_NAME='internship-lambda-policy'
    FILE_IAM_POLICY_DOC="${DIR_IAM_POLICY_DOC}/${IAM_POLICY_DOC_NAME}.json" \
        && echo ${FILE_IAM_POLICY_DOC}

## 2. メインの処理

### 2.1. ポリシー作成

    aws iam create-policy \
        --policy-name ${IAM_POLICY_NAME} \
        --path "${IAM_POLICY_PATH}" \
        --policy-document file://${FILE_IAM_POLICY_DOC} \
        --description "${IAM_POLICY_DESCRIPTION}"

## 3. 確認

### 3.1. ポリシーの存在確認

ポリシー名が表示されたらOK

    aws iam list-policies \
        --scope Local \
        --path-prefix "${IAM_POLICY_PATH}" \
        --max-items 1000 \
        --query "Policies[?PolicyName == \`${IAM_POLICY_NAME}\`].PolicyName" \
        --output text
