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
	stage('Deploy to Dev'){
    sh 'mv target/*.war target/myweb.war'
    
sshagent(['tomcatconn']) {
    // some block
    sh 'ssh -oStrictHostKeyChecking=no ec2-user@172.31.88.152'
    sh 'ssh ec2-user@172.31.88.152 rm -rf /opt/tomcat8/webapps/myweb*'
    sh 'scp target/myweb.war ec2-user@172.31.88.152:/opt/tomcat8/webapps'
    sh 'ssh ec2-user@172.31.88.152 sudo service tomcat restart'
}
    slackSend channel: '#developers',
				  color: 'good',
				  message: "Job -  ${env.JOB_NAME}, Completed successfully Build URL is ${env.BUILD_URL}" 
}}}
catch(error){
  slackSend channel: '#developers',
				  color: 'danger',
				  message: "Job -  ${env.JOB_NAME}, Failed, Build URL is ${env.BUILD_URL}"
   error 'Something wrong'
}
