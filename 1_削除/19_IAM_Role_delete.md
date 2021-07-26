<!-- omit in toc -->
# IAMロールを削除

## 1. 変数の指定

### 1.1. ロール名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    IAM_ROLE_NAME='internship-lambda-role'-${ORIGINNAME}

## 2. メインの処理

### 2.1. ロールの削除

    aws iam delete-role \
       --role-name ${IAM_ROLE_NAME}

## 3. 確認

### 3.1. ロールの確認

ロール名が表示されなければOK

    aws iam list-roles \
        --query "Roles[?RoleName == \`${IAM_ROLE_NAME}\`].RoleName" \
        --output text
