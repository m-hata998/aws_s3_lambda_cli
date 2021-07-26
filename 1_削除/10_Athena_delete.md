<!-- omit in toc -->
# Athenaの環境削除

## 1. 変数の指定

### 1.1. リージョン指定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. データベース名

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`
    DATABASE_NAME='interndatabase'_${ORIGINNAME}

### 1.3. テーブル名

    TABLE_NAME='personcount'

### 1.4. データグループ名

    DATAGROUP_NAME='interndatagroup'-${ORIGINNAME}

## 2. メインの処理

### 2.1. テーブル削除

    aws glue delete-table \
        --database-name ${DATABASE_NAME} \
        --name ${TABLE_NAME}

### 2.2. データベース削除

    aws glue delete-database \
        --name ${DATABASE_NAME}

### 2.3. ワークグループ削除

    aws athena delete-work-group \
        --work-group ${DATAGROUP_NAME} \
        --recursive-delete-option

## 3. 確認

### 3.1. データベース・テーブル確認

出力がなければOK

    aws athena list-databases \
        --catalog-name AwsDataCatalog \
        --query "DatabaseList[?Name == '${DATABASE_NAME}'].Name" \
        --output text

### 3.2. ワークグループ確認

出力がなければOK

    aws athena list-work-groups \
        --query "WorkGroups[?Name == '${DATAGROUP_NAME}'].Name" \
        --output text
