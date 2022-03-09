node {
   def commit_id
   stage('Preparation') {
     checkout scm
     sh "git rev-parse --short HEAD > .git/commit-id"                        
     commit_id = readFile('.git/commit-id').trim()
   }
   stage('Test') {
     nodejs(nodeJSInstallationName: 'nodejs') {
       sh 'npm install'	 
       sh 'npm test'
     }
   }
   
   stage('Deploy') {
     nodejs(nodeJSInstallationName: 'nodejs') {
       sh 'sudo rync -av * /nodejs1'	 
     }
   }
   
   stage('Docker Build & Push') {
     docker.withRegistry('https://index.docker.io/v2/', 'dockerhub') {
		def app = docker.build("mraagil/docker-nodejs:${commit_id}", '.').push()
     }
   }
   
}