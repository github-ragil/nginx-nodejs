node {
   def commit_id
   stage('Checkout Git') {
     checkout scm
     sh "git rev-parse --short HEAD > .git/commit-id"                        
     commit_id = readFile('.git/commit-id').trim()
   }

   stage('Installing dependencies') {
     nodejs(nodeJSInstallationName: 'nodejs') {
       sh 'npm install'	 
     }	 
   }

   stage('Testing') {
     nodejs(nodeJSInstallationName: 'nodejs') {
       sh 'npm test'	 
     }
   }

   stage('Compile Changes') {  
       sh 'sudo rsync -av * /nodejs1'
   }
   
   stage('Docker Build & Push') {
     docker.withRegistry('https://index.docker.io/v2/', 'dockerhub') {
		def app = docker.build("mraagil/docker-nodejs", '.').push()
     }
   }
   
   stage('Docker Pull & Deploy Scale Out') {
		def app = docker.build("mraagil/docker-nodejs", '.').pull()
		sh 'sudo bash deploy.sh'
		
   }
   stage('Push Notification') {
steps {
script{
withCredentials([string(credentialsId: ‘telegramToken’, variable: ‘TOKEN’),
string(credentialsId: ‘telegramChatId’, variable: ‘CHAT_ID’)]) {
sh ”””
curl -s -X POST https://api.telegram.org/bot${TOKEN}/sendMessage -d chat_id=${CHAT_ID} -d parse_mode=”HTML” -d text=”<b>Project</b> : POC \
<b>Branch</b>: master \
<b>Build </b> : OK \
<b>Test suite</b> = Passed”
”””
}
}
}
}		
   
}