try{
node {
    
    properties([parameters([choice(choices: ['master', 'develo-1', 'develo-2', 'slave', 'qa'], description: 'select the branch to process', name: 'Branch')])])
    
    stage('git checkout'){
   git credentialsId: 'github4', url: 'https://github.com/javahometech/my-app'
   //branch: "${params.gitBranch}"
    }
    stage('maven build'){
    def mavenhome = tool name: 'maven3', type: 'maven'
    def mvncmd = "${mavenhome}/bin/mvn"
        sh  "${mvncmd} clean package"
    }

stage('Build docker image'){

sh 'docker build -t arunodayraja/my-app:2.0.0 .'

}
stage('push docker image'){


withCredentials([string(credentialsId: 'dockerpw1', variable: 'dockerhubpw')]) {
    sh "docker login -u arunodayraja -p ${dockerhubpw}"
}


sh 'docker push arunodayraja/my-app:2.0.0'

}

stage('run container on the dev server'){
def dockerrun = 'docker run -p 8080:8080 -d --name my-app arunodayraja/my-app:2.0.0'

sshagent(['tomcatcode1']) {
    sh "sudo ssh -o StrictHostKeyChecking=no ec2-user@172.31.88.152 ${dockerrun}"
}
}}}
catch(error){
  slackSend channel: '#developers',
				  color: 'danger',
				  message: "Job -  ${env.JOB_NAME}, Failed, Build URL is ${env.BUILD_URL}"
   error 'Something wrong'
}
