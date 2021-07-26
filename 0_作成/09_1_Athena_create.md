<!-- omit in toc -->
# Athenaの環境作成

## 1. 変数の指定

### 1.1. リージョン指定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. S3バケット名

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

### 1.3. データベース名

    DATABASE_NAME='interndatabase'_${ORIGINNAME}

### 1.4. テーブル名

    TABLE_NAME='personcount'

### 1.5. データグループ名

    DATAGROUP_NAME='interndatagroup'-${ORIGINNAME}

### 1.6. テーブル作成用ドキュメント名

    DIR_ATHENA_DOC="${HOME}/environment/conf-athena"

#### 1.6.1. ディレクトリの存在確認

    ls -d ${DIR_ATHENA_DOC}

ディレクトリが存在しない（[ls: cannot access XXXXX : No such file or directory]と表示される場合  
下記を実行しディレクトリ作成

    mkdir -p ${DIR_ATHENA_DOC}

### 1.7. ドキュメントファイル名

    ATHENA_DOC_NAME='create-table'
    FILE_ATHENA_DOC="${DIR_ATHENA_DOC}/${ATHENA_DOC_NAME}.json" \
        && echo ${FILE_ATHENA_DOC}

### 1.8. テーブル作成用ドキュメント

    cat << EOF > ${FILE_ATHENA_DOC}
    {
        "Name": "${TABLE_NAME}",
        "StorageDescriptor": {
            "Columns": [
                {
                    "Name": "filename",
                    "Type": "string"
                },
                {
                    "Name": "time",
                    "Type": "string"
                },
                {
                    "Name": "content",
                    "Type": "map<string,int>"
                }
            ],
            "Location": "s3://${S3_BUCKET_NAME}/json",
            "InputFormat": "org.apache.hadoop.mapred.TextInputFormat",
            "OutputFormat": "org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat",
            "SerdeInfo": {
                "SerializationLibrary": "org.openx.data.jsonserde.JsonSerDe",
                "Parameters": {
                    "serialization.format": "1"
                }
            }
        },
        "Parameters": {
            "EXTERNAL": "TRUE",
            "has_encrypted_data": "false",
            "serialization.encoding": "utf-8"
        }
    }
    EOF

### 1.9. JSONフォーマットの確認

エラーが出力されなければOK

    cat ${FILE_ATHENA_DOC} \
        |  python3 -m json.tool \
        > /dev/null

## 2. メインの処理

### 2.1. データベース作成

    aws glue create-database --database-input "{\"Name\":\"${DATABASE_NAME}\"}"

### 2.2. テーブル作成

    aws glue create-table \
        --database-name ${DATABASE_NAME} \
        --table-input file://${FILE_ATHENA_DOC}

### 2.3. ワークグループ作成

    aws athena create-work-group \
        --name ${DATAGROUP_NAME} \
        --configuration ResultConfiguration={OutputLocation="s3://${S3_BUCKET_NAME}/athena/"},EnforceWorkGroupConfiguration="true",PublishCloudWatchMetricsEnabled="true"

## 3. 確認

### 3.1. データベース確認

データベース名が出力できたらOK

    aws athena list-databases \
        --catalog-name AwsDataCatalog \
        --query "DatabaseList[?Name == '${DATABASE_NAME}'].Name" \
        --output text

### 3.2. テーブル確認

テーブル名が出力されたらOK

    aws athena list-table-metadata \
        --catalog-name AwsDataCatalog \
        --database-name ${DATABASE_NAME} \
        --query 'TableMetadataList[].Name' \
        --output text

### 3.3. ワークグループ確認

グループ名が出力されたらOK

    aws athena list-work-groups \
        --query "WorkGroups[?Name == '${DATAGROUP_NAME}'].Name" \
        --output text
