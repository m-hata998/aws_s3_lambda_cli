<!-- omit in toc -->
# ECR削除

ECRリポジトリにプッシュしたイメージを削除します

## 1. 変数の指定

### 1.1. リージョンの指定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. リポジトリ名の指定

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    ECR_REPOSITORY_NAME='interncontainer'-${ORIGINNAME}

## 2. メインの処理

### 2.1 ECR削除

    aws ecr delete-repository \
        --repository-name ${ECR_REPOSITORY_NAME}

## 3. 確認

### 3.1. ECRリポジトリ存在確認

[An error occurred (RepositoryNotFoundException) when calling the DescribeRepositories operation: The repository with name XXXXX does not exist in the registry with id XXXXXXXXXXXX] のエラーが出力されたらOK

    aws ecr describe-repositories \
        --repository-name ${ECR_REPOSITORY_NAME} \
        --query 'repositories[].repositoryName' \
        --output text
