pipeline {
    agent any
    
    parameters { 
         string(name: 'tomcat_staging', defaultValue: '10.0.2.7', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '10.0.2.9', description: 'Production Server')
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

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                   steps {
                        build job: 'Course2_Deploy_to_Staging'
                    }
                }

                stage ('Deploy to Production'){
                    steps{
                        timeout(time:5, unit:'DAYS'){
                            input message:'Approve Production Deployment?'
                        }

                        build job: 'Course2_Deploy_to_Prod'
                    }
                    post {
                        success {
                            echo 'Code successfully deployed to Production'
                        }

                        failure {
                            echo 'Deployment to Production has failed'
                        }
                    }    
                }
            }
        }
        
    }
}