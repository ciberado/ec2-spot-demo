# EC2 instances spot demo

## Install the CLI

```bash
sudo apt update
sudo apt upgrade
sudo apt install python3-pip -y
pip3 install awscli
aws --version
```

```bash
aws configure
```

## Create the bootstrapping script

```bash
cat << EOF > userdata.sh
#!/bin/sh

wget https://github.com/ciberado/pokemon/releases/download/stress/pokemon-0.0.4-SNAPSHOT.jar
java -jar pokemon-0.0.4-SNAPSHOT.jar
EOF
```

## Launch your instances

```bash
```
aws ec2 run-instances \
  --image-id ami-00890f614e48ce866 \
  --count 3 \
  --instance-type c4.4xlarge \
  --instance-market-options \
    '{"MarketType":"spot", "SpotOptions" : { "SpotInstanceType" : "one-time"}}' \
  --subnet-id "subnet-0f00f589b56094f8c" \
  --security-group-ids sg-0377cfcbc040a880c \
  --associate-public-ip-address \
  --user-data file://userdata.sh \
  --tag-specifications \
    'ResourceType=instance,Tags=[{Key=Name,Value=demo}]' 'ResourceType=volume,Tags=[{Key=Name,Value=demo}]' 
```

## Monitor status

```bash
aws ec2 describe-instances \
  --filters "Name=tag:Name,Values=demo"    \
  --query \ "Reservations[*].Instances[*].{Instance:InstanceId,State:State.Name,IP:PublicIpAddress,AZ:Placement.AvailabilityZone,Name:Tags[?Key=='Name']|[0].Value}"   \
  --output table
 ```
 
 
