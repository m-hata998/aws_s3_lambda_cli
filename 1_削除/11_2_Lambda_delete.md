<!-- omit in toc -->
# Lambda関数削除

※動作確認済
Lambda関数を削除します。

## 1. 変数の指定

### 1.1. リージョンの指定

    # export AWS_DEFAULT_REGION='ap-northeast-1'
    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. Lambda関数名の指定

    ORIGINFILEPATH='/home/ec2-user/environment/originname'
    ORIGINNAME=`cat ${ORIGINFILEPATH}`

    LAMBDA_FUNCTION_NAME='internship-yolo5'-${ORIGINNAME}

## 2. メインの処理

### 2.1. 関数の削除

    aws lambda delete-function \
        --function-name ${LAMBDA_FUNCTION_NAME}

## 3. 確認

### 3.1. 関数の存在確認

出力がなければOK

    aws lambda list-functions \
        --query "Functions[?FunctionName == \`${LAMBDA_FUNCTION_NAME}\`].FunctionName" \
        --output text
