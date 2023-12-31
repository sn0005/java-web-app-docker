node{
     
    stage('SCM Checkout'){
        git url: 'https://github.com/sn0005/java-web-app-docker.git',branch: 'master'
    }
    
    stage(" Maven Clean Package"){
      def mavenHome =  tool name: "Maven", type: "maven"
      def mavenCMD = "${mavenHome}/bin/mvn"
      sh "${mavenCMD} clean package"
      
    } 
    
    
    stage('Build Docker Image'){
        sh 'docker build -t dockerhandson/java-web-app .'
    }
    
    stage('Push Docker Image'){
        withCredentials([string(credentialsId: 'dockerhub_key', variable: 'DockerHub_key')]) {
          sh "docker login -u sn0005 -p ${DockerHub_key}"
        }
        sh 'docker push sn0005/java-web-app'
     }
     
     stage('SSH into machine') {
          steps {
               withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                         failonError: true,
                         continueOnError: false,
                         publishers: [
                              sshPublisherDesc(
                                   configName: jenkinsserver,
                                   sshCredentials: [
                                        username: "$USERNAME",
                                        encryptedPassphrase: "$USERPASS"
                                        ],
                                   {sh "docker run  -d -p 8080:8080 --name java-web-app sn0005/java-web-app" }
                                    )
                              ]
                         )
               }
          }
     }
}
