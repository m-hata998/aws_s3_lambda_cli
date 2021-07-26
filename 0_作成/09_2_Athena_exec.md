<!-- omit in toc -->
# Athenaの実行

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

### 1.5. Query

    QUERY_STRING="SELECT * FROM ${DATABASE_NAME}.${TABLE_NAME} limit 10"  

## 2. メインの処理

### 2.1. Query実行

    QUERY_ID=$( \
        aws athena start-query-execution \
            --query-string "${QUERY_STRING}" \
            --work-group ${DATAGROUP_NAME} \
            --query 'QueryExecutionId' \
            --output text
    ) \
    && echo ${QUERY_ID}

### 2.2. Query実行結果確認

    aws athena get-query-results --query-execution-id ${QUERY_ID} \
        --query 'ResultSet.Rows'

## 3. 確認

2.2で結果が出力されればOK
