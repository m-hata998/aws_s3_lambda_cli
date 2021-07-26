<!-- omit in toc -->
# ORIGINNAMEの指定

複数人での作業時、構築する要素を一意に特定するための名前をファイルに指定  
Athenaでのデータベース名に使用する関係上、文字列のみにする方が良いです（-を含むのはNG）

## 1. 変数の指定

### 1.1. ファイルパス

    ORIGINFILEPATH='/home/ec2-user/environment/originname'

### 1.2. ORIGINNAMEとして使用する値を設定

SETORIGINNAME''の中に、一意になる（他の人と重複しない）値を設定

    SETORIGINNAME=''

## 2. メインの処理

### 2.1. ファイル作成

    cat <<EOF> ${ORIGINFILEPATH}
    ${SETORIGINNAME}
    EOF

## 3. 確認

### 3.1. バケットの存在確認

SETORIGINNAMEで設定した値と一致すればOK

    echo `cat ${ORIGINFILEPATH}`
