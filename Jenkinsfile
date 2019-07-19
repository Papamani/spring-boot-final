pipeline{
    agent any
    stages{
     stage ('build'){
            steps{
               
                    sh "mvn clean package"
               
            }
        }
        stage ('test'){
            steps{
               
                    sh "mvn test"
               
            }
        }
    stage('Sonar') 
       {environment {
           scannerHome=tool 'sonar scanner'
       }
            steps {
                
                sh "mvn sonar:sonar -Dsonar.host.url=http://3.14.251.87:9000"
            }
        }
        stage ('Nexus'){
            steps{
 withCredentials([usernamePassword(credentialsId: 'sudipa_nexus', passwordVariable: 'pwd_2', usernameVariable: 'usr')]) {
sh label: '', script: 'curl -u $usr:$pwd_2 --upload-file target/myWebApp_Test-0.0.1-SNAPSHOT.war http://3.14.251.87:8081/nexus/content/repositories/devopstraining/Subha_Nexus_Test_Spring/myWebApp_Test-0.0.1-SNAPSHOT.war'
}
            
        }
        }
         stage ('Deploy'){
            steps{
                 withCredentials([usernamePassword(credentialsId: 'devops-tomcat', passwordVariable: 'pass', usernameVariable: 'userId')]) {
              //withCredentials([usernameColonPassword(credentialsId: 'Tomcat server secret key', variable: 'password1')]) {
     //echo "My password is '${password1}'!"
                    sh "cd target;ls"
                    sh label: '', script:'curl -u $userId:$pass http://ec2-18-224-182-74.us-east-2.compute.amazonaws.com:8080/manager/text/undeploy?path=/Subha_Spring_Test_1'
                    sh label: '', script: 'curl -u  $userId:$pass --upload-file target/myWebApp_Test-0.0.1-SNAPSHOT.war http://ec2-18-224-182-74.us-east-2.compute.amazonaws.com:8080/manager/text/deploy?config=file:/var/lib/tomcat8/myWebApp_Test-0.0.1-SNAPSHOT.war\\&path=/Subha_Spring_Test_1'
            }
        }

    }
}
/*post { 
        success{ 
            echo 'notified to slack'
            slackSend baseUrl:'https://digitaldevopselite.slack.com', color: 'good', iconEmoji: '', message: 'your ${env.BUILD_NUMBER} success', username: 'Subhasanket Satapathy'
        }
    }*/
     post {
   /* success {
      slackSend (color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }
    failure {
      slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
    }*/
    always {
            echo 'I will always say Hello again!'
            
            emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
                recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
            
        }
  }
}

