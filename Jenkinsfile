pipeline{
   agent any
   parameters {
    string(
      description: 'Run flyway database migration using latest master branch from prices in what environment?',
      name: 'tagversion',
      defaultValue: 'myown',
      trim: false


    )
  }
   
     stages{
        stage(checkout){
         steps{
               git 'https://github.com/srihari437/sparkjava-war-example.git'
            }
      }
       stage('build image'){
          steps{
            sh "docker build -t srihari437/sunday:${params.tagversion} ."
           }
      }
  
     stage('image push'){
       steps{
      withCredentials([string(credentialsId: 'hubpass', variable: 'hubpass')]) {
             sh 'docker login -u srihari437 -p ${hubpass}'
             sh "docker push srihari437/sunday:${params.tagversion}"
             }
           }
       }
     stage('deploy'){
          steps{
               sh 'chmod +777 changetag.sh'
               
        sshagent(['k8deploy']) {
        sh 'scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@13.232.136.108:/home/ec2-user'
         script{
             try{
             sh 'ssh ec2-user@13.232.136.108 kubectl apply -f .'
            }catch(error) {
             sh 'ssh ec2-user@13.232.136.108 kubectl create -f .'
                    }
                   }
                }
       }
      }
   }
}
