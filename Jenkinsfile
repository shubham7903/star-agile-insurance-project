pipeline {
    agent { label 'jappbuildserver1' }	

    tools {
        maven "maven_3.6.3"
    }

	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	} 
    
    stages {
        stage('SCM Checkout') {
            steps {
                git 'https://github.com/LoksaiETA/BankingApp.git'
            }
		}
        stage('Maven Build') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
		}
       stage("Docker build"){
            steps {
				sh 'docker version'
				sh "docker build -t shubhamxoxo/insure-me:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag shubhamxoxo/insure-me:${BUILD_NUMBER} shubhamxoxo/insure-me:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}
		stage('Push2DockerHub') {

			steps {
				sh "docker push shubhamxoxo/insure-me:latest"
			}
		}
        stage('Deploy to Kubernetes Dev Environment') {
            steps {
		script {
		sshPublisher(publishers: [sshPublisherDesc(configName: 'Kubernetes', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubernetesdeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
		       }
            }
    	}
    }
}
