<!-- omit in toc -->
# Lambda用IAMロールのポリシードキュメント作成

Lambda関数に付与するポリシードキュメントを作成します。

## 1. 変数の指定

### 1.1. リージョン指定

    # export AWS_DEFAULT_REGION='ap-northeast-1'
    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. ディレクトリ指定

    DIR_IAM_POLICY_DOC="${HOME}/environment/conf-lambda"

### 1.3. ドキュメント名

    IAM_POLICY_DOC_NAME='internship-lambda-policy'
    FILE_IAM_POLICY_DOC="${DIR_IAM_POLICY_DOC}/${IAM_POLICY_DOC_NAME}.json" \
        && echo ${FILE_IAM_POLICY_DOC}

### 1.4. AWSID

    AWS_ID=$( \
        aws sts get-caller-identity \
            --query 'Account' \
            --output text \
    ) \
    && echo ${AWS_ID}

### 1.5. ロググループ名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    LOGS_GROUP_NAME='/aws/lambda/internship-yolo5'-${ORIGINNAME}

### 1.6. S3バケット名

    S3_BUCKET_INPUT_PREFIX='raspberrypi-input'
    S3_BUCKET_INPUT_NAME="${S3_BUCKET_INPUT_PREFIX}-${AWS_ID}-${ORIGINNAME}" \
        && echo ${S3_BUCKET_INPUT_NAME}

    S3_BUCKET_OUTPUT_PREFIX='raspberrypi-output'
    S3_BUCKET_OUTPUT_NAME="${S3_BUCKET_OUTPUT_PREFIX}-${AWS_ID}-${ORIGINNAME}" \
        && echo ${S3_BUCKET_OUTPUT_NAME}

## 2. メインの処理

### 2.1. ポリシードキュメント作成

    cat << EOF > ${FILE_IAM_POLICY_DOC}
    {
        "Version": "2012-10-17",
        "Statement": [
            {
            "Action": [
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:logs:*:${AWS_ID}:log-group:${LOGS_GROUP_NAME}:*"
            ]
            },
            {
                "Action": [
                    "s3:GetObject",
                    "s3:GetObjectACL"
                ],
                "Effect": "Allow",
                "Resource": [
                    "arn:aws:s3:::${S3_BUCKET_INPUT_NAME}/*"
                ]
            },
            {
                "Action": [
                    "s3:PutObject",
                    "s3:PutObjectACL"
                ],
                "Effect": "Allow",
                "Resource": [
                    "arn:aws:s3:::${S3_BUCKET_OUTPUT_NAME}/*"
                ]
            }
        ]
    }
    EOF

    cat ${FILE_IAM_POLICY_DOC}

### 2.2. JSONフォーマットの確認

エラーが出力されなければOK

    cat ${FILE_IAM_POLICY_DOC} \
        |  python3 -m json.tool \
        > /dev/null

## 3. 確認

### 3.1. ファイルの存在確認

ファイルのパスが表示されたらOK

    ls ${FILE_IAM_POLICY_DOC}
