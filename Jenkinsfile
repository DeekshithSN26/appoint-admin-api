pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
       
        stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                             sh '''
                                docker build -t deekshithsn/adminapi:${VERSION} .
                                docker login -u deekshithsn -p $docker_password
                                docker push  deekshithsn/adminapi:${VERSION}
                                docker rmi deekshithsn/adminapi:${VERSION}
                            '''
                    }
                }
            }
        }

        stage('Deploying application on k8s cluster') {
            steps {
               script{
                   withCredentials([kubeconfigFile(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]) {
                        
                          sh '''
                          sed -i "s,IMAGENAME,deekshithsn/adminapi:${VERSION},g" admin-api.yaml
                          kubectl apply -f admin-api.yaml
                          ''' 
                        
                    }
               }
            }
        }

    }
}
