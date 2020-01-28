
pipeline {    
    agent { 
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    triggers {
        pollSCM('*/15 * * * *')
    }
    options { disableConcurrentBuilds() }
    stages {
        stage('Permissions') {
            steps {
                sh 'chmod 775 *'
            }
        }
stage('Cleanup') {
            steps {
                sh 'mvn clean'
            }
        }
       /* stage('Check Style, FindBugs, PMD') {
            steps {
                sh './gradlew --no-daemon checkstyleMain checkstyleTest findbugsMain findbugsTest pmdMain pmdTest cpdCheck'
            }
        post {
        always {
                step([
                $class         : 'FindBugsPublisher',
                pattern        : 'build/reports/findbugs/*.xml',
                canRunOnFailed : true
                ])
                step([
                $class         : 'PmdPublisher',
                pattern        : 'build/reports/pmd/*.xml',
                canRunOnFailed : true
                ])
                step([
                $class: 'CheckStylePublisher', 
                pattern: 'build/reports/checkstyle/*.xml',
                canRunOnFailed : true
                ])
        }
      }
    } */
stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'build/test-results/test/*.xml'
                }
            }
        }
        stage('Build') {
            steps {
                sh 'mvn build'
            }
        }
        stage('Update Docker UAT image') {
            when { branch "master" }
            steps {
                sh '''
					docker login -u "tejpalborkar10" -p "tejpal123"
                    docker build --no-cache -t spring-boot-websocket-chat-demo .
                    docker tag spring-boot-websocket-chat-demo:latest tejpalborkar10/spring-boot-websocket-chat-demo:latest
                    docker push tejpalborkar10/spring-boot-websocket-chat-demo:latest
					docker rmi tejpalborkar10/spring-boot-websocket-chat-demo:latest
                '''
            }
        }
        stage('Update UAT container') {
            when { branch "master" }
            steps {
                sh '''
					docker pull tejpalborkar10/spring-boot-websocket-chat-demo:latest
                    docker stop spring-boot-websocket-chat-demo
                    docker rm spring-boot-websocket-chat-demo
                    docker run -p 9090:9090 --name spring-boot-websocket-chat-demo -t -d tejpalborkar10/spring-boot-websocket-chat-demo:latest
                    docker rmi -f $(docker images -q --filter dangling=true)
                '''
            }
        }
        stage('Release Docker image') {
            when { buildingTag() }
            steps {
                sh '''
					docker login -u "tejpalborkar10" -p "tejpal123"
                    docker build --no-cache -t spring-boot-websocket-chat-demo .
                    docker tag person:latest tejpalborkar10/spring-boot-websocket-chat-demo:${TAG_NAME}
                    docker push tejpalborkar10/spring-boot-websocket-chat-demo:${TAG_NAME}
					docker rmi $(docker images -f "dangling=true" -q)
               '''
            }
        }
    }
}