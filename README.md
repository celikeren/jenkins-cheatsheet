# docker

## creating image manually

- `docker run -dit openjdk:8-jdk-alpine`

- `docker container exec <cid> ls /tmp`

- `docker images`

- `docker container ls`

- `docker container cp .\target\dummy-project.jar cid:/tmp`  
    >_copy_


- `docker container exec <c_id> ls /tmp`  
    > _we check if copy successful_


- `docker container commit --change='CMD ["java","-jar","/tmp/hello-world-rest-api.jar"]' practical_solomon in28min/hello-world-rest-api:manual1` 
    > _saves the container we creataed as an image_
    _in28min... -> repo name we want to give_
    _change: jar file will be launched at startup_

- `docker images`

- `docker run in28min/hello-world-rest-api:manual1`


## Dockerfile commands

- `FROM openjdk:8-jdk-alpine`  
    > _base image_  
    _a Dockerfile must start with FROM_  
    _FROM can appear multiple times within a single Dockerfile_  
    _a name can be given to a new build stage by adding AS name_  

- `ADD target/hello-world-rest-api.jar hello-world-rest-api.jar`
    > _ADD copies files or directories_  
    _ADD \<src> \<dest>_  
    _ADD and COPY are functionally similar, generally COPY is preferred_  
    _COPY only supports the basic copying of local files, while ADD has some extra features_  
- `ENTRYPOINT ["sh", "-c", "java - jar /hello-world-rest-api.jar"]`  
    > _ENTRYPOINT has two forms:_  
    _ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)_  
    _ENTRYPOINT command param1 param2 (shell form)_  
    _configures a container that will run as an executable_  
    _sh means shell_  
    _\-c means commands are read from string_  
    
    ....devam edecek
    
    
    
    
    
    
    
    
    
    











------pipeline script---------

instead of git conf in project
and
build - execute shell:

docker build -t dummy-eren .
echo eren > deneme
docker run -tid -p 9090:8080 dummy-eren

pipeline: (Jenkinsfile)


pipeline {
    agent { label 'master' }
    stages {
        stage('git clone') {
            steps {
                sh 'rm -rf dummy-spring-boot; git clone https://gitlab.com/celikeren/dummy-spring-boot.git'
            }
        }
        stage('build') {
            steps {
                sh 'docker build -t erencelik/dummy-eren /var/lib/jenkins/workspace/eren'
            }
        }
        stage('run') {
            steps {
                sh 'docker run -tid -p 9090:8080 erencelik/dummy-eren'
            }
        }
        stage('push') {
            steps {
                sh 'docker push erencelik/dummy-eren'
            }
        }
    }
}





    
    
    





-------------------jenkins--------------------------------

ci (continous integration) tool
amacı entegrasyon
otomasyon aracı
projenin derlenmesi, testlerin çalışması, uygulamanın dağıtılmasını otomatize edebilmektedir

--gitlab ci (credentials yönetme zor, docker in docker) 
--travis (güzelliği debug var)


config.xml
workspace


pipeline / free style projects


credentials
    ssh key



docker pull jenkins/jenkins:2.214-centos

run -p 8080:8080 -p 50000:50000 jenkins/jenkins:2.214-centos

jenkins'in içine docker kurmak gerekiyor.
Dockerfile'ı çalıştırabilmesi için.






    
    
    
    
    
    
    
    
    
    
    
    
