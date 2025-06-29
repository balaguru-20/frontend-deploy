pipeline{
    agent { label 'AGENT-1' } //jenkins node name
    environment{
        PROJECT = 'expense'     //We can use these as global variables
        COMPONENT = 'frontend' 
        DEPLOY_TO = "production"
        REGION = "us-east-1"
        appVersion = ''
        environment = ''
    }
    options{
        disableConcurrentBuilds()  //Builds will not applt at a time
        //timeout(time: 5, unit: 'SECONDS') //timeout after some metioned time
        timeout(time: 30, unit: 'MINUTES') // generally 30 min or based on project
    }
     parameters{
        string(name: 'version', description: 'Enter the application version')
        choice(name: 'deploy_to', choices: ['dev', 'qa', 'prod'], description: 'Pick something')
    }
    stages{
        stage('Setup Environment'){
            steps{
                script{
                    appVersion = params.version
                    environment = params.deploy_to
                }
            }
        }
        stage('Deploy'){
            steps{
                script{
                    withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                        sh """
                            aws eks update-kubeconfig --region us-east-1 --name expense-dev
                            kubectl get nodes
                            cd helm
                            sed -i 's/IMAGE_VERSION/${params.version}/g' values-${environment}.yaml
                            helm upgrade --install $COMPONENT -n $PROJECT -f values-${environment}.yaml .
                        """
                    }
                }
            }
        } 
    }
     
    post{
        always{
            echo 'I will always say Hello again!'
            deleteDir() //it will deletes the directory
        }
        failure{
            echo 'I will run when pipeline is failed'
        }
        success{
            echo 'I will run when pipeline is success'
        }
    }
}