<!-- omit in toc -->
# Lambda関数作成

Lambdaの関数を作成します。

## 1. 変数の指定

### 1.1. リージョンの指定

    # export AWS_DEFAULT_REGION='ap-northeast-1'
    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. Lambda関数名の指定

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`

    LAMBDA_FUNCTION_NAME='internship-yolo5'-${ORIGINNAME}

### 1.3. Lambda関数の説明

    LAMBDA_FUNCTION_DESCRIPTION='function for yolo5'

### 1.4. IAMロール名

    IAM_ROLE_NAME='internship-lambda-role'-${ORIGINNAME}

### 1.5. IAMのARN取得

    IAM_ROLE_ARN=$( \
        aws iam get-role \
            --role-name ${IAM_ROLE_NAME} \
            --query 'Role.Arn' \
            --output text \
    ) \
    && echo ${IAM_ROLE_ARN}

### 1.6. ECRイメージ名

    ECR_REPOSITORY_NAME='interncontainer'-${ORIGINNAME}

### 1.7. ECRパスの指定

    AWS_ID=$( \
        aws sts get-caller-identity \
            --query 'Account' \
            --output text \
    ) \
    && echo ${AWS_ID}

    ECR_PATH=${AWS_ID}'.dkr.ecr.'${AWS_DEFAULT_REGION}'.amazonaws.com'

### 1.8. ECRのダイジェストの取得

    DIGEST=$(aws ecr list-images --repository-name ${ECR_REPOSITORY_NAME} \
        --out text \
        --query 'imageIds[?imageTag==`latest`].imageDigest') \
    && echo ${DIGEST}

### 1.9. メモリサイズ

    MEMORY_SIZE=1024

### 1.10. タイムアウト時間

    TIMEOUT_SECONDS=900

## 2. メインの処理

### 2.1. 関数の作成

    aws lambda create-function \
        --function-name ${LAMBDA_FUNCTION_NAME} \
        --description "${LAMBDA_FUNCTION_DESCRIPTION}" \
        --package-type Image \
        --code ImageUri=${ECR_PATH}/${ECR_REPOSITORY_NAME}@${DIGEST} \
        --role ${IAM_ROLE_ARN} \
        --memory-size ${MEMORY_SIZE} \
        --timeout ${TIMEOUT_SECONDS}

## 3. 確認

### 3.1. 関数の存在確認

関数名が表示されたらOK

    aws lambda list-functions \
        --query "Functions[?FunctionName == \`${LAMBDA_FUNCTION_NAME}\`].FunctionName" \
        --output text

### 3.2. メモリサイズの確認

1.9.で設定したメモリサイズになっていればOK

    aws lambda list-functions \
        --query "Functions[?FunctionName == \`${LAMBDA_FUNCTION_NAME}\`].MemorySize" \
        --output text

### 3.3. タイムアウト秒の確認

1.10.で設定したタイムアウト秒になっていればOK

    aws lambda list-functions \
        --query "Functions[?FunctionName == \`${LAMBDA_FUNCTION_NAME}\`].Timeout" \
        --output text
