pipeline {
    agent {
        docker {
            image 'maven:3.8.4-openjdk-17'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    tools {
        maven 'maven3'
    }
    environment {
        SONARQUBE_SCANNER_HOME = tool 'sonarqube'
        SONAR_TOKEN = credentials('sonarqube-token')
        SONAR_HOST_URL = 'http://172.17.0.3:9000'
    }
    stages{
        stage('Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'Github_cred',
                    url: 'https://github.com/nikitaks97/spring-webapp-sonar.git'
            }
        }
        /*stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests --no-transfer-progress  -Denforcer.skip=true'
            }
        }*/
        stage('Test') {
            steps {
                sh 'mvn test --no-transfer-progress  -Denforcer.skip=true'
            }
        }
        stage('SonarQube Analysis') {
            steps {
             withSonarQubeEnv('sonarqube-server') {
             sh'mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.token=${SONAR_TOKEN} --no-transfer-progress  -Denforcer.skip=true'
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 20, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package -DskipTests --no-transfer-progress  -Denforcer.skip=true'
            }

           } 
        }
    }