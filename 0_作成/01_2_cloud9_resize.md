<!-- omit in toc -->
# Cloud9の容量拡張

dockerコンテナ作成用にCloud9(EBS)の容量を拡張します

## 1. 変数の指定

### 1.1. リージョン指定

    export AWS_DEFAULT_REGION='us-west-2'

### 1.2. resizeスクリプト

    RESIZE_SHELL='/home/ec2-user/environment/resize.sh'

### 1.2. resizeスクリプト出力

AWS公式のドキュメントにあるresizeスクリプト

    cat <<\EOF> ${RESIZE_SHELL}
    #!/bin/bash

    # Specify the desired volume size in GiB as a command line argument. If not specified, default to 20 GiB.
    SIZE=${1:-20}

    # Get the ID of the environment host Amazon EC2 instance.
    INSTANCEID=$(curl http://169.254.169.254/latest/meta-data/instance-id)

    # Get the ID of the Amazon EBS volume associated with the instance.
    VOLUMEID=$(aws ec2 describe-instances \
      --instance-id $INSTANCEID \
      --query "Reservations[0].Instances[0].BlockDeviceMappings[0].Ebs.VolumeId" \
      --output text)

    # Resize the EBS volume.
    aws ec2 modify-volume --volume-id $VOLUMEID --size $SIZE

    # Wait for the resize to finish.
    while [ \
      "$(aws ec2 describe-volumes-modifications \
        --volume-id $VOLUMEID \
        --filters Name=modification-state,Values="optimizing","completed" \
        --query "length(VolumesModifications)"\
        --output text)" != "1" ]; do
    sleep 1
    done

    #Check if we're on an NVMe filesystem
    if [ $(readlink -f /dev/xvda) = "/dev/xvda" ]
    then
      # Rewrite the partition table so that the partition takes up all the space that it can.
      sudo growpart /dev/xvda 1

      # Expand the size of the file system.
      # Check if we are on AL2
      STR=$(cat /etc/os-release)
      SUB="VERSION_ID=\"2\""
      if [[ "$STR" == *"$SUB"* ]]
      then
        sudo xfs_growfs -d /
      else
        sudo resize2fs /dev/xvda1
      fi

    else
      # Rewrite the partition table so that the partition takes up all the space that it can.
      sudo growpart /dev/nvme0n1 1

      # Expand the size of the file system.
      # Check if we're on AL2
      STR=$(cat /etc/os-release)
      SUB="VERSION_ID=\"2\""
      if [[ "$STR" == *"$SUB"* ]]
      then
        sudo xfs_growfs -d /
      else
        sudo resize2fs /dev/nvme0n1p1
      fi
    fi
    EOF

### 1.3. 拡張後の容量(GB)

    EXTEND_VOLUME_GB=30

## 2. メインの処理

### 2.1. EBSの拡張

出力がない場合は時間をおいて再実行

    sh ${RESIZE_SHELL} ${EXTEND_VOLUME_GB}

## 3. 確認

### 3.1. 容量確認

    df -h

Mount On[/]の容量が指定したサイズになっていることを確認
