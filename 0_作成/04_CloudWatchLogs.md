<!-- omit in toc -->
# CloudWatch Logsのロググループ作成

Lambdaのログを出力するCloudWatch Logsのロググループを作成します。

## 1. 変数の指定

### 1.1. リージョン指定

    # export AWS_DEFAULT_REGION='ap-northeast-1'
    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. ロググループ名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    LOGS_GROUP_NAME='/aws/lambda/internship-yolo5'-${ORIGINNAME}

### 1.3. 保持期間

    RETENTION_DAY=1

## 2. メインの処理

### 2.1. ロググループ作成

    aws logs create-log-group \
        --log-group-name ${LOGS_GROUP_NAME}

### 2.2. 保持期間変更

    aws logs put-retention-policy \
        --log-group-name ${LOGS_GROUP_NAME} \
        --retention-in-days ${RETENTION_DAY}

## 3. 確認

### 3.1. ロググループの存在確認

出力があればOK

    aws logs describe-log-groups \
        --log-group-name-prefix ${LOGS_GROUP_NAME} \
        --query 'logGroups[].logGroupName' \
        --output text

### 3.2. 保持期間確認

1.3.で指定した日数になっていればOK

    aws logs describe-log-groups \
        --log-group-name-prefix ${LOGS_GROUP_NAME} \
        --query 'logGroups[].retentionInDays' \
        --output text
