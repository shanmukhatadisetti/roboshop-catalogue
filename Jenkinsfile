pipeline{
    agent {
        label 'AGENT-1'
    }
    environment{
        appVersion=''
    }
    options{
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
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
    }
    post{
        always{
            deleteDir()
        }
    }
}