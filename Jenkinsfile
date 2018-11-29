//Jenkinsfile
node {

stage('Preparation') {
      //Installing kubectl in Jenkins agent
      sh 'curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl'
  sh 'chmod +x ./kubectl && mv kubectl /usr/local/sbin'

//Clone git repository
   git url:'https://github.com/tomiollila/training1.git'
   }

stage('Integration') {
 
      withKubeConfig([credentialsId: 'jenkins-deploy1', serverUrl: 'https://kubernetes.default']){ 
        //sh 'kubectl create cm nodejs-app --from-file=src/ --namespace=castorlabsdev -o=yaml --dry-run > deploy/cm.yaml'
          sh 'kubectl apply -f /home/jenkins/workspace/training-jenkins-kubernetes/deploy/nodejs.yaml --namespace=castorlabsdev'
         sh 'kubectl apply -f /home/jenkins/workspace/training-jenkins-kubernetes/deploy/nginx-reverseproxy.yaml --namespace=castorlabsdev'}
          sleep 30
           //sh 'kubectl get svc --namespace=castorlabsdev| grep nginx-reverseproxy-service| grep -o -E '[0-9]+'| grep 31'}//, returnStdout: true
stage('Tests') {
 //Executing tests
 println("Executing tests")
     sh 'chmod +x /home/jenkins/workspace/training-jenkins-kubernetes/tests/integration-tests.sh && /home/jenkins/workspace/training-jenkins-kubernetes/tests/integration-tests.sh'
}  

stage('CleanUp') {
     //Cleaning the integration environment
     println("Cleaning integration environment...")
     sh 'kubectl delete deployment nginx-reverseproxy-deployment --namespace=castorlabsdev'
      sh 'kubectl delete deployment nodejs-deployment --namespace=castorlabsdev'
         println("Integration stage finished.")   
    }                      
}
