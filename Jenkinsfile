import groovy.json.JsonSlurperClassic

def jsonParse(def json) {
    new groovy.json.JsonSlurperClassic().parseText(json)
}
pipeline {
    agent any
    stages {
        stage("Paso 1: Compliar"){
            steps {
                script {
                sh "echo 'Compile Code!!!!'"
                // Run Maven on a Unix agent.
                sh './mvnw clean compile -e'
                }
            }
        }
        stage("Paso 2: Testear"){
            steps {
                script {
                sh "echo 'Test Code!'"
                // Run Maven on a Unix agent.
                sh './mvnw clean test -e'
                }
            }
        }
        stage("Paso 3: Build .Jar"){
            steps {
                script {
                sh "echo 'Build .Jar!'"
                // Run Maven on a Unix agent.
                sh './mvnw clean package -e'
                }
            }
            post {
                //record the test results and archive the jar file.
                success {
                    archiveArtifacts artifacts:'build/*.jar'
                }
            }
        }
        stage("Paso 4: Análisis SonarQube"){
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "echo 'Calling sonar Service in another docker container!'"
                    // Run Maven on a Unix agent to execute Sonar.
                    sh './mvnw clean verify sonar:sonar -Dsonar.projectKey=custom-project-key -Dsonar.projectName=custom-project-key'
                }
            }
        }
        stage('STAGE TEST') {
            steps {
                script{
                   env.STAGE='STAGE TEST'
                   env.channel='D0457H2RV9A'
                   
                 }
            }
			post{
				success{
					slackSend color: 'good', channel: "${env.channel}", message: "[Mentor Devops] [${JOB_NAME}] [${BUILD_TAG}] Ejecucion Exitosa", teamDomain: 'devopsusach20-lzc3526', tokenCredentialId: 'token-slack'
				}
				failure{
					slackSend color: 'danger',channel: "${env.channel}", message: "[Mentor Devops] [${env.JOB_NAME}] [${BUILD_TAG}] Ejecucion fallida en stage [${env.STAGE}]", teamDomain: 'devopsusach20-lzc3526', tokenCredentialId: 'token-slack'
				}
			}
        }
    }
    }
    post {
        always {
            sh "echo 'fase always executed post'"
        }
        success {
            sh "echo 'fase success'"
        }

        failure {
            sh "echo 'fase failure'"
        }
    }
}
