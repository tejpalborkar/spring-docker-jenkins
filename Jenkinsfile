
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
         /*   post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            } */
        }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Update Docker UAT image') {
            when { branch "master" }
            steps {
                sh '''
                curl -fsSLO https://get.docker.com/builds/Linux/x86_64/docker-17.04.0-ce.tgz \
  && tar xzvf docker-17.04.0-ce.tgz \
  && mv docker/docker /usr/local/bin \
  && rm -r docker docker-17.04.0-ce.tgz
					docker login -u "tejpalborkar10" -p "tejpal123"
                    docker build --no-cache -t spring-docker-jenkins .
                    docker tag spring-docker-jenkins:latest tejpalborkar10/spring-docker-jenkins:latest
                    docker push tejpalborkar10/spring-docker-jenkins:latest
					docker rmi tejpalborkar10/spring-docker-jenkins:latest
                '''
            }
        }
        stage('Update UAT container') {
            when { branch "master" }
            steps {
                sh '''
					docker pull tejpalborkar10/spring-docker-jenkins:latest
                  	 docker stop  $(docker images -q tejpalborkar10/spring-docker-jenkins)
                  	 docker rm  $(docker images -q tejpalborkar10/spring-docker-jenkins)
                    docker run --rm -d -p 5000:8080 --name spring-docker-jenkins -t -d tejpalborkar10/spring-docker-jenkins:latest
                    docker rmi -f $(docker images -q --filter dangling=true)
                '''
            }
        }
        stage('Release Docker image') {
            when { buildingTag() }
            steps {
                sh '''
					docker login -u "tejpalborkar10" -p "tejpal123"
                    docker build --no-cache -t spring-docker-jenkins .
                    docker tag person:latest tejpalborkar10/spring-docker-jenkins:${TAG_NAME}
                    docker push tejpalborkar10/spring-docker-jenkins:${TAG_NAME}
					docker rmi $(docker images -f "dangling=true" -q)
               '''
            }
        }
    }
}