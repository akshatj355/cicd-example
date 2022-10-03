def DOCKER_HUB_USER="akshatj355"
def HTTP_PORT="80"
def IMAGE_NAME=JOB_NAME
def IMAGE_TAG=BUILD_NUMBER

pipeline {
    environment {
    repo_URL = "https://github.com/akshatj355/cicd-example"      #Replace it with yours
  }
  agent any
    stages {
        stage('Checkout Repository') {
            steps {
                git branch: 'main', credentialsId: 'akshatj355', url: repo_URL
                sh 'ls'
                sh 'cd webServer'
            }
        }
        stage('Building Docker Image') {
            steps {
                sh 'echo "Creating DockerFile...."'
                sh '''echo "FROM nginx
                            LABEL maintainer="akshatj355@gmail.com"
                            COPY . /usr/share/nginx/html" >> Dockerfile
                   '''
                sh 'echo "Building Docker Image...."'   
                imageBuild(IMAGE_NAME, IMAGE_TAG)
            }
        }
        stage('Push to Docker Registry'){
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerHubAccount', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                pushToImage(IMAGE_NAME, IMAGE_TAG, USERNAME, PASSWORD)
                }
            }
       }
       stage('Running/Deploying Application to QA'){
           steps {
               runApp("application-qa", IMAGE_TAG, DOCKER_HUB_USER, HTTP_PORT)
           }
       }
    }
}

def imageBuild(ImageName, tag){
    sh "docker build -t $ImageName:$tag --pull --no-cache ."
    echo "Image Build Complete..."
}

def pushToImage(ImageName, tag, dockerUser, dockerPassword){
    sh "docker login -u $dockerUser -p $dockerPassword"
    sh "docker tag $ImageName:$tag $dockerUser/$ImageName:$tag"
    sh "docker push $dockerUser/$ImageName:$tag"
    echo "Image Push Complete..."
}

def runApp(ImageName, tag, dockerHubUser, httpPort){
    sh "docker pull $dockerHubUser/$ImageName"
    sh "docker run -it -d -p $httpPort:$httpPort --name $ImageName $dockerHubUser/$ImageName:$tag"
    echo "Application started on port: ${httpPort} (http)..."
}
