# jenkins cheatsheet

## project configuration for git and docker  

- `docker run -p 8080:8080 jenkins/jenkins`  
  
    > _we download and run jenkins in docker_  
  
    _after initialization a long password is printed_  
    _login on localhost:8080 using this password_  
  
- install suggested plugins  
  
- select new item & freestyle project  
    
    - source code management  
    _we can match the jenkins item with a repo_  
    _and easily manage our source code_  
  
    - build  
    _we can add a build step here_  
    _for example we can build the image from Dockerfile and run it:_  
        - execute shell  
            `docker build -t dummy-project .`  
            `docker run -tid -p 5000:8080 dummy-project`  


## pipeline for git and docker  

- new pipeline  

- pipeline script:  
_we can configure the build step by step here_  
_stage simply indicates a higher level sequence than steps_  
_stages shown as columns in pipeline stage view with time and status_  

_for 1st stage, we can use git checkout command instead of removing folder and cloning it again_  
  
`pipeline {
    agent { label 'master' }
    stages {
        stage('git clone') {
            steps {
                sh 'rm -rf dummy-project; git clone https://gitlab.com/celikeren/dummy-project.git'
            }
        }
        stage('build') {
            steps {
                sh 'docker build -t erencelik/dummy-project /var/lib/jenkins/workspace/dummy-project'
            }
        }
        stage('run') {
            steps {
                sh 'docker run -tid -p 5000:8080 erencelik/dummy-project'
            }
        }
        stage('push') {
            steps {
                sh 'docker push erencelik/dummy-project'
            }
        }
    }
}
`

_here we cloned the git repo, built a docker image,_  
_ran it as a container and pushed it to docker hub_  
__you don't need to do the same things in every deploy__  
__just tell jenkins once and it handles the rest__  


## Jenkinsfile  

_a step further_  
_we can make it easier with a Jenkinsfile in the project_  


`String gitOrigin = "https://gitlab.com/celikeren/dummy-project.git"
String dockerHub = "erencelik/dummy-project"
node{
properties([
    parameters([
        gitParameter(branch: '',
                     defaultValue: '1.0',
                     description: '',
                     useRepository: "${gitOrigin}",
                     name: 'tag',
                     quickFilterEnabled: false,
                     selectedValue: 'NONE',
                     sortMode: 'NONE',
                     tagFilter: '*',
                     branchFilter: 'origin/(.*)',
                     type: 'PT_TAG')
    ])
])
   stage ('Checkout') {
   checkout([$class: 'GitSCM', branches: [[name: 'tags/${tag}']], doGenerateSubmoduleConfigurations: false,gitTool: 'Default', extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '162a1630-56da-4614-82c1-7f07dc0d8751', url: "${gitOrigin}"]]]) 
  }
 
    stage("Build Dockerfile"){
         sh """
         docker build -t ${dockerHub}:${tag} .
         """
    }
    stage("Push Docker Image"){
           sh """
           docker push ${dockerHub}:${tag} 
          """
    }
    
}
`
