<!-- omit in toc -->
# S3のオブジェクト削除(output)

※動作確認済  
Raspberry piからのoutput用に作成したS3バケットを削除します。

## 1. 変数の指定

### 1.1. S3バケット名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`

    S3_BUCKET_PREFIX='raspberrypi-output'

    AWS_ID=$( \
        aws sts get-caller-identity \
            --query 'Account' \
            --output text \
    ) \
    && echo ${AWS_ID}

    S3_BUCKET_NAME="${S3_BUCKET_PREFIX}-${AWS_ID}-${ORIGINNAME}" \
      && echo ${S3_BUCKET_NAME}

## 2. メインの処理

### 2.1. バケット削除

    aws s3 rb s3://${S3_BUCKET_NAME} \
        --force

## 3. 確認

### 3.1. バケットの存在確認

出力がなければOK

    aws s3 ls \
        | grep ${S3_BUCKET_NAME} \
        | awk '{print $3}'
