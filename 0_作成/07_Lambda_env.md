<!-- omit in toc -->
# Lambdaの環境変数設定

Lambdaの関数に環境変数（YOLO５の実行結果出力先）を作成します。

## 1. 変数の指定

### 1.1. リージョンの指定

    # export AWS_DEFAULT_REGION='ap-northeast-1'
    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. Lambda関数名の指定

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`

    LAMBDA_FUNCTION_NAME='internship-yolo5'-${ORIGINNAME}

### 1.3. S3バケット名

    S3_BUCKET_PREFIX='raspberrypi-output'

    AWS_ID=$( \
        aws sts get-caller-identity \
            --query 'Account' \
            --output text \
    ) \
    && echo ${AWS_ID}

    S3_BUCKET_NAME="${S3_BUCKET_PREFIX}-${AWS_ID}-${ORIGINNAME}" \
      && echo ${S3_BUCKET_NAME}

### 1.4. 環境変数の設定値

    STRING_LAMBDA_FUNCTION_ENVIRONMENT="Variables={OUTPUT_BUCKET=${S3_BUCKET_NAME},OUTPUTJSON_PATH='json',OUTPUT_PATH='image'}"

## 2. メインの処理

### 2.1. 環境変数の設定

    aws lambda update-function-configuration \
        --function-name ${LAMBDA_FUNCTION_NAME} \
        --environment "${STRING_LAMBDA_FUNCTION_ENVIRONMENT}"

## 3. 確認

### 3.1. 環境変数の設定値確認

3種類の値が設定されていればOK

    aws lambda get-function-configuration \
        --function-name ${LAMBDA_FUNCTION_NAME} \
        --query 'Environment.Variables'
