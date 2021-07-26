<!-- omit in toc -->
# IAMロールからポリシーをデタッチ

## 1. 変数の指定

### 1.1. ロール名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    IAM_ROLE_NAME='internship-lambda-role'-${ORIGINNAME}

### 1.2. ポリシー名

    IAM_POLICY_NAME='internship-lambda-policy'-${ORIGINNAME}

### 1.3. ポリシーパス

    IAM_POLICY_PATH='/internship-cli/'

### 1.4. ARN取得

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

### 2.1. ポリシーのデタッチ

    aws iam detach-role-policy \
        --role-name ${IAM_ROLE_NAME} \
        --policy-arn ${IAM_POLICY_ARN}

## 3. 確認

### 3.1. ポリシーの確認

ポリシー名が表示されなければOK

    aws iam list-attached-role-policies \
        --role-name ${IAM_ROLE_NAME} \
        --query "AttachedPolicies[?PolicyName == \`${IAM_POLICY_NAME}\`].PolicyName" \
        --output text
