# Devops_docker
# Docker installation
## install docker
```apt-get install -y docker.io```
## start Docker with the systemctl command
```systemctl start docker```
## Enable docker to run at system boot:
```systemctl enable docker```
## check the docker version
```docker version```
# USAGE --- creating containers
## search for a base image with the docker search command
```docker search ubuntu```
## download the base image to our server
```docker pull ubuntu```
## see all downloaded images
```docker images```
## To create the container
```docker create ubuntu:16.04``` this creates a container using the downloaded image and the version needed.
**Note:** the above 'docker create' command will create a new container **but not start it**
## To create a container and start it immediately so you can run commands
```docker run -i -t ubuntu:16.04 /bin/bash```
## To exit the container
```exit``` **note:** The container will stop when you leave it with the command exit
## If you like to have a container that is running in the background, you just need to add the -d option in the command.
```docker run -i -t -d ubuntu:16.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"```
### Now you can see the container running in the background by using command
```docker ps```
### if you want to see the logs result from that bash command
```docker logs NAMES/ContainerID```
e.g
```docker logs abcddadadadedaswf```
### How can I access the shell of container that runs in the background mode?
```docker exec -i -t abcddadadadedaswf /bin/bash```
## stop container without deleting it
```docker stop abcddadadadedaswf```
## start container
```docker start abcddadadadedaswf```
## If you like to remove the container, stop it first and then remove it
```docker rm abcddadadadedaswf```


# Install ssh to a running docker container
```
# apt-get update && apt-get install -y openssh-server net-tools nano
# mkdir /var/run/sshd
# chmod 0755 /var/run/sshd
# useradd --create-home --shell /bin/bash --groups sudo popsonebz
# passwd popsonebz
# ifconfig   -------to check the ip address
# passwd root
# nano /etc/ssh/sshd_config
change the line 
permitLoginRoot yes
# service ssh restart
```
### Now you can ssh into the container
```ssh popsonebz@0.0.0.0 -p 2222``` or ```ssh popsonebz@172.17.0.2 -p 2222``` or
```ssh root@0.0.0.0 -p 2222```
**Note** your docker must expose port 22 to other containers and mapp port 22 to your host for the outside world to connect to the container e.g

```docker run -i -t -d -p 2222:22 --expose=22 ubuntu:16.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"```

# Working with Amazon AWS S3 using python
## Setting the environment
```
from boto.s3.connection import S3Connection
access_key = 'abananabsnaasan'
secret_key = 'jjajjajashsjashajsasasasasa'
conn = S3Connection(access_key, secret_key)
mybucket = conn.get_bucket('qltrail-lab-265-1500692331') 
```
the above is assuming you have created a bucket via aws console called qltrail-lab-265-1500692331 . The bucket contains objects i.e folders and files (pdf, picture, txt etc).

## To list object names i.e filename or directory name in a bucket
```folders = mybucket.list("","/")```

```
for folder in folders:
    print folder.name
```
## To list files in a specific folder called 'lab' which is in a bucket
```
for obj in mybucket.list('lab'):
    print obj.name # or obj.key
```
## Using boto3 is better
### method 1
```
import boto3
client = boto3.client(
    's3',
    aws_access_key_id=ACCESS_KEY,
    aws_secret_access_key=SECRET_KEY,
    aws_session_token=SESSION_TOKEN,
)
```
### method 2
```
from boto3.session import Session

ACCESS_KEY='your_access_key'
SECRET_KEY='your_secret_key'

session = Session(aws_access_key_id=ACCESS_KEY,
                  aws_secret_access_key=SECRET_KEY)
s3 = session.resource('s3')
your_bucket = s3.Bucket('your_bucket')

for s3_file in your_bucket.objects.all():
    print(s3_file.key)
```
### method 3
```
import boto3

access_key='your_access_key'
secret_key='your_secret_key'

s3 = boto3.client('s3',aws_access_key_id=access_key,aws_secret_access_key=secret_key)
```
##### listing all bucket in your s3
```
response = s3.list_buckets() 

for bucket in response['Buckets']:
    print bucket['Name']
````
