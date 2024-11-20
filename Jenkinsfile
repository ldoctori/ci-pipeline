pipeline {
    parameters {
        string(name: 'branch', defaultValue: 'develop', description: 'Ветка')
    }
    stage('Source') {
        git branch: parameters.branch,
        credentialsId: 'jenkins_github_access',
        url: "git@github.com:ldoctori/${artifactId}.git"
    }
    stage('Build') {
       withMaven{
           sh "mvn clean install"
       }
       env.IMAGE_NAME = "ldoctori/${artifactId}-docker-registry:${env.BUILD_ID}"
       docker.build(env.IMAGE_NAME)
    }

    stage('Deploy to DockerHub') {
        withDockerRegistry(credentialsId: "jenkins_docerhub_access") {
            docker.image(env.IMAGE_NAME).push()
       }
    }

    stage('Clean') {
        cleanWs()
        sh "cd ~/.m2/repository; rm -rf *"
        sh "docker rmi ${env.IMAGE_NAME}"
    }

}