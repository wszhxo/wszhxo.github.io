```sh
#!/bin/bash


GIT_REP_PATH=/home/dev/repository/minio
GIT_REP_PROJECT=/home/dev/repository/minio

JAR_PATH=/home/dev/client
JAR_NAME=minio-client.jar

git_rep_update(){
    cd "$GIT_REP_PATH"
	git clean -f
	git checkout master_zhx
	git clean -f
	git pull
}

build_service()
{
    cd "$GIT_REP_PATH"
	mvn clean package
}

restart_server()
{
cd $JAR_PATH
ps -ax | grep ${JAR_PATH}/${JAR_NAME} | grep -v grep | awk '{print $1}' | xargs kill
pwd
nohup java -server -Xmx512m -Xms512m -jar $JAR_PATH/$JAR_NAME --spring.profiles.active=dev >/dev/null 2>&1 &
echo restartFinish!
}

copy_file(){
    cp $GIT_REP_PROJECT/target/$JAR_NAME $JAR_PATH/$JAR_NAME
	echo copyFileFinish!
}

git_rep_update
build_service
copy_file
restart_server


```

