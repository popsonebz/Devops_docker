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
##### creating Bucket
```
s3.create_bucket(Bucket='my-bucket')
```
##### updload file
```
filename = 'me.txt'
bucket_name = 'qltrail-lab-265-1500692331'
s3.upload_file(filename, bucket_name,filename)
```
##### check if it is uploaded
```
s3 = boto3.resource('s3',aws_access_key_id=access_key,aws_secret_access_key=secret_key)
bucket = s3.Bucket('qltrail-lab-265-1500692331')

for obj in bucket.objects.filter(Delimiter="/"):
    print(obj.key)

for obj in bucket.objects.filter(Prefix='lab'): #return keys starting with 'lab'
    print(obj.key)

for obj in bucket.objects.filter(): #return every possible key
    print(obj.key)
```
##### sync local folder to s3
- install s3cmd to your local server

```
$sudo apt-get install s3cmd
```
- configure s3cmd with your credential
```
$s3cmd --configure
Access Key: AXXXXXXXXXX
Secret Key: AXXXXXXXXXXXXXXXXXXXXXXXXX
Default Region [US]:
Encryption password: fisosososossososo
Path to GPG program [/usr/bin/gpg]:
Use HTTPS protocol [Yes]:
```
- create your folder and add content locally
```
$mkdir s3FolderTest
$touch s3FolderTest/file1.txt
$touch s3FolderTest/file2.txt
```
- list all bucket path in s3
```
$s3cmd ls
2017-07-22 02:58  s3://qltrail-lab-265-xxxxxxxx
```
- uploading a single file with s3cmd but renaming it on s3
```
$s3cmd put file1.txt s3://s3tools-demo/my_renamed_file.txt
```
- to sync a complete folder 
```
$s3cmd put --recursive s3FolderTest s3://qltrail-lab-265-xxxxxxxx/test/ #this will create 'test' folder if it does not exist. inside it, it will store create a folder called s3FolderTest with its content
```
- **note** if you specify s3FolderTest/ as below:
```
$s3cmd put --recursive s3FolderTest/ s3://qltrail-lab-265-xxxxxxxx/test/ 
this will dump the content of s3FolderTest into the folder 'test'
```
- using sync command

sync first checks the list and details of the files already present at the destination, compares with the local files and only uploads the ones that either are not present remotely or have a different size or md5 checksum.
```
$touch s3FolderTest/file3.txt
$s3cmd sync s3FolderTest s3://qltrail-lab-265-xxxxxxxxx/test/
upload: 's3FolderTest/file4.txt' -> 's3://qltrail-lab-265-1500692331/test/s3FolderTest/file3.txt'  [1 of 1]
 0 of 0     0% in    0s     0.00 B/s  done
```
