<!-- omit in toc -->
# IAMポリシーを削除

## 1. 変数の指定

### 1.1. ポリシー名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    IAM_POLICY_NAME='internship-lambda-policy'-${ORIGINNAME}

### 1.2. ポリシーパス

    IAM_POLICY_PATH='/internship-cli/'

### 1.3. ARN取得

    IAM_POLICY_ARN=$( \
        aws iam list-policies \
            --scope Local \
            --path-prefix "${IAM_POLICY_PATH}" \
            --max-items 1000 \
            --query "Policies[?PolicyName==\`${IAM_POLICY_NAME}\`].Arn" \
            --output text \
    ) \
        && echo "${IAM_POLICY_ARN}"

## 2. メインの処理

### 2.1. ポリシーの削除

    aws iam delete-policy \
        --policy-arn ${IAM_POLICY_ARN}

## 3. 確認

### 3.1. ポリシーの確認

ポリシー名が表示されなければOK

    aws iam list-policies \
        --scope Local \
        --path-prefix "${IAM_POLICY_PATH}" \
        --max-items 1000 \
        --query "Policies[?PolicyName == \`${IAM_POLICY_NAME}\`].PolicyName" \
        --output text
