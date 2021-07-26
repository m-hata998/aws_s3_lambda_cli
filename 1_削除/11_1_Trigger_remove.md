<!-- omit in toc -->
# トリガー削除

Lambda関数のトリガーを削除します。

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

## 2. メインの処理

### 2.1. S3のイベントに対する権限削除

    aws lambda remove-permission \
        --function-name ${LAMBDA_FUNCTION_NAME} \
        --statement-id "s3-put-event"

### 2.2. S3の通知削除

    aws s3api put-bucket-notification-configuration \
        --bucket ${S3_BUCKET_NAME} \
        --notification-configuration "{}"

## 3. 確認

### 3.1. Lambdaの権限削除確認

[An error occurred (ResourceNotFoundException) when calling the GetPolicy operation: The resource you requested does not exist.]と表示されたらOK

    aws lambda get-policy \
        --function-name ${LAMBDA_FUNCTION_NAME} \
        --query 'Policy[]'

### 3.2. S3の通知削除確認

何も表示されなければOK

    aws s3api get-bucket-notification-configuration \
        --bucket ${S3_BUCKET_NAME}  \
        --output text
