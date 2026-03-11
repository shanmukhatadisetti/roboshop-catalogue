pipeline{
    agent {
        label 'AGENT-1'
    }
    environment{
        appVersion = ''
        REGION = "us-east-1"
        ACC_ID = "430774481266"
        PROJECT= "roboshop"
        COMPONENT = "catalogue"
    }
    options{
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    parameters {
        booleanParam(name: 'Deploy', defaultValue: false, description: 'Toggle this value')
    }
    stages{
        stage('Read Package.json'){
            steps{
                script{
                    def packageJSON = readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "Package Version:${appVersion}"
                }
            }
        }
        stage('Install Dependencies'){
            steps{
                script{
                    sh """
                        npm install
                    """
                }
            }
        }
        stage('Docker Build'){
            steps{
                script{
                    withAWS(credentials: 'aws-cred', region: 'us-east-1') {
                        sh """
                            aws ecr get-login-password --region ${REGION} | docker login --username AWS --password-stdin ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com
                            docker build -t ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                            docker push ${ACC_ID}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                        """
                    }
                }
            }
        }
        stage('Trigger Deploy'){
             when {
                expression { params.Deploy }
            }
            steps{
                script{
                    build job: 'catalogue-cd',
                     parameters: [
                          string(name: 'appVersion', value: "${appVersion}")
                      ],
                    propagate: false,
                    wait: false
                }
            }
        }
    }
    post{
        always{
            deleteDir()
        }
    }
}