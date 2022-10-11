pipeline {
    agent any
    
    tools {
        maven 'maven 3.8.6'
    }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Archiving the artifacts'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }
        stage("static code analasis"){
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'Sonar_credentials')
                    sh "mvn clean package sonar:sonar"
                }
            }
        }
        stage("Quality gate status"){
            steps{
                script{
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar_credentials'
                }
            }
        }
        stage ('Deployments'){
            parallel{
                stage ("Deploy to Staging"){
                    steps {
                        deploy adapters: [tomcat7(credentialsId: 'tomcat_credentials', path: '', url: 'http://13.232.81.39:8080/')], contextPath: null, war: '**/*.war'
                    }
                }
            }
        }
    }
}
