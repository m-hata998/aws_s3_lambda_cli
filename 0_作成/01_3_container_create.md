<!-- omit in toc -->
# dockerコンテナ作成

dockerコンテナを作成します

## 1. 変数の指定

### 1.1. リージョンの指定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. リポジトリ名

    GIT_REPOSITORY='https://github.com/m-hata998/yolov5-lambda'

### 1.3. ECRパスの指定

    AWS_ID=$( \
        aws sts get-caller-identity \
            --query 'Account' \
            --output text \
    ) \
    && echo ${AWS_ID}

    ECR_PATH=${AWS_ID}'.dkr.ecr.'${AWS_DEFAULT_REGION}'.amazonaws.com'

### 1.4. リポジトリ名の指定

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    ECR_REPOSITORY_NAME='interncontainer'-${ORIGINNAME}

## 2. メインの処理

### 2.1. gitからソースをpull

    git clone ${GIT_REPOSITORY}

### 2.2. pythonライブラリインストール

    cd ./yolov5-lambda/yolov5
    pip install -r requirements.txt

### 2.3. dockerコンテナの作成

ECRにアップロードするdockerコンテナを作成します。

    cd ../ 
    
    docker build -t ${ECR_REPOSITORY_NAME} .

### 2.4. dockerイメージへタグ付与

    docker tag ${ECR_REPOSITORY_NAME}:latest ${ECR_PATH}/${ECR_REPOSITORY_NAME}:latest

### 2.5. ECRの認証

    aws ecr get-login-password | docker login --username AWS --password-stdin ${ECR_PATH}

### 2.6. ECRへのプッシュ

    docker push ${ECR_PATH}/${ECR_REPOSITORY_NAME}:latest
