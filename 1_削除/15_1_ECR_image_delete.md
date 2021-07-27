<!-- omit in toc -->
# ECR削除

ECRリポジトリを削除します

## 1. 変数の指定

### 1.1. リージョンの指定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. リポジトリ名の指定

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    ECR_REPOSITORY_NAME='interncontainer'-${ORIGINNAME}

## 2. メインの処理

### 2.1 イメージ削除

latestしかない前提

    aws ecr batch-delete-image \
        --repository-name ${ECR_REPOSITORY_NAME} \
        --image-ids imageTag=latest

## 3. 確認

### 3.1. ECRイメージ存在確認

出力がなければOK

    aws ecr list-images \
        --repository-name ${ECR_REPOSITORY_NAME} \
        --query 'imageIds' \
        --output text
