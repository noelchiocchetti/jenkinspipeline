pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_dev', defaultValue: '35.166.210.154', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '34.209.233.6', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') 
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deploy to Staging'){
            steps {
                build job: 'Course2_Deploy_to_Staging'
            }
        }

        stage('Deploy to Production') {
            timeout(time:5, unit:'DAYS'){
                input message:'Approve Production Deployment?'
            }

            build job: 'Course2_Deploy_to_Prod'
        }
        post{
            success {
                echo 'Code successfully deployed to Production'
            }

            failure{
                echo 'Deployment to Production has failed'
            }
        }
    }
}