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
}}
catch(error){
  slackSend channel: '#developers',
				  color: 'danger',
				  message: "Job -  ${env.JOB_NAME}, Failed, Build URL is ${env.BUILD_URL}"
   error 'Something wrong'
}
