<!-- omit in toc -->
# S3のオブジェクト作成(input)

Raspberry piからのinput用にS3バケットを作成します。

## 1. 変数の指定

### 1.1. S3バケット名

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

### 1.2. リージョン指定

    # S3_BUCKET_LOCATION="ap-northeast-1"
    S3_BUCKET_LOCATION="us-west-2"

## 2. メインの処理

### 2.1. バケット作成

    aws s3api create-bucket \
        --bucket ${S3_BUCKET_NAME} \
        --create-bucket-configuration "LocationConstraint=${S3_BUCKET_LOCATION}"

## 3. 確認

### 3.1. バケットの存在確認

出力があればOK

    aws s3api list-buckets \
        --query "Buckets[?Name == \`${S3_BUCKET_NAME}\`].Name" \
        --output text

### 3.2. バケットのリージョン確認

1.2で指定したリージョンになっていればOK

    S3_BUCKET_LOCATION=$( \
        aws s3api get-bucket-location \
            --bucket ${S3_BUCKET_NAME} \
            --output text \
    ) \
    && echo ${S3_BUCKET_LOCATION}
