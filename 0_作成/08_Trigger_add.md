<!-- omit in toc -->
# トリガー追加

Lambdaの関数にトリガーを追加します。  
※Prefixの指定が必要な場合はS3APIにて設定

## 1. 変数の指定

### 1.1. リージョンの指定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. S3バケット名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`

    S3_BUCKET_PREFIX='raspberrypi-input'

    AWS_ID=$( \
        aws sts get-caller-identity \
            --query 'Account' \
            --output text \
    ) \
    && echo ${AWS_ID}

    S3_BUCKET_NAME="${S3_BUCKET_PREFIX}-${AWS_ID}-${ORIGINNAME}" \
      && echo ${S3_BUCKET_NAME}

### 1.3. Lambda関数名

    LAMBDA_FUNCTION_NAME='internship-yolo5'-${ORIGINNAME}

### 1.4. Lambda ARN

    LAMBDA_RESOURCE_NAME="arn:aws:lambda:${AWS_DEFAULT_REGION}:${AWS_ID}:function:${LAMBDA_FUNCTION_NAME}" \
    && echo ${LAMBDA_RESOURCE_NAME}

### 1.5. S3conf

    S3_CONF_FILE='s3conf'
    DIR_CONF_DOC="${HOME}/environment/conf-lambda"
    S3_CONF_DOC="${DIR_CONF_DOC}/${S3_CONF_FILE}.json" \
        && echo ${S3_CONF_DOC}

    cat << EOF > ${S3_CONF_DOC}
    {
        "LambdaFunctionConfigurations": [
            {
                "LambdaFunctionArn": "${LAMBDA_RESOURCE_NAME}",
                "Events": [
                    "s3:ObjectCreated:*"
                ]
            }
        ]
    }
    EOF

### 1.6. JSONフォーマットの確認

エラーが出力されなければOK

    cat ${S3_CONF_DOC} \
        |  python3 -m json.tool \
        > /dev/null

## 2. メインの処理

### 2.1. S3のイベントに対する権限設定

    aws lambda add-permission \
        --function-name ${LAMBDA_FUNCTION_NAME} \
        --statement-id "s3-put-event" \
        --action "lambda:InvokeFunction" \
        --principal "s3.amazonaws.com" \
        --source-arn "arn:aws:s3:::${S3_BUCKET_NAME}" \
        --source-account ${AWS_ID}

### 2.2. S3の通知追加

※エラーになる場合は時間をおいて再実行

    aws s3api put-bucket-notification-configuration \
        --bucket ${S3_BUCKET_NAME} \
        --notification-configuration file://${S3_CONF_DOC}

## 3. 確認

### 3.1. Lambdaの権限追加確認

    aws lambda get-policy \
        --function-name ${LAMBDA_FUNCTION_NAME} \
        --query 'Policy'

### 3.2. S3の通知追加確認

ファンクション名が表示されることを確認

    aws s3api get-bucket-notification-configuration \
        --bucket ${S3_BUCKET_NAME}  \
        --query "LambdaFunctionConfigurations[].LambdaFunctionArn" \
        --output text
