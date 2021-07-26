<!-- omit in toc -->
# CloudWatch Logsのロググループ削除

※動作確認済  
CloudWatch Logsのロググループを削除します。

## 1. 変数の指定

### 1.1. リージョン指定

    # export AWS_DEFAULT_REGION='ap-northeast-1'
    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. ロググループ名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    LOGS_GROUP_NAME='/aws/lambda/internship-yolo5'-${ORIGINNAME}

## 2. メインの処理

### 2.1. ロググループ作成

    aws logs delete-log-group \
        --log-group-name ${LOGS_GROUP_NAME}

## 3. 確認

### 3.1. ロググループの存在確認

出力がなければOK

    aws logs describe-log-groups \
        --log-group-name-prefix ${LOGS_GROUP_NAME} \
        --query 'logGroups[].logGroupName' \
        --output text
