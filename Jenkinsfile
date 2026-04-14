pipeline {
    agent { label 'slave-1' }
    tools {
        maven 'Maven'
    }
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/ashinde222/project.git'
            }
        }
        stage('Update DB Config') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'db-creds',
                    usernameVariable: 'DB_USER',
                    passwordVariable: 'DB_PASS'
                )]) {

                    sh '''
                    FILE=$(find . -name "userRegistration.jsp")

                    sed -i "s|localhost:3306|YOUR-RDS-ENDPOINT|g" $FILE
                    sed -i "s|root|$DB_USER|g" $FILE
                    sed -i "s|1234|$DB_PASS|g" $FILE
                    '''
                }
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Deploy') {
             steps {
             sshagent(['ssh-key']) {
             sh '''
                scp -o StrictHostKeyChecking=no target/LoginWebApp.war ec2-user@3.110.115.64:/home/ec2-user/
                ssh -o StrictHostKeyChecking=no ec2-user@3.110.115.64 "sudo mv /home/ec2-user/LoginWebApp.war /mnt/server/apache-tomcat-10.1.54/webapps/"
                '''
                        }
                    }
                }
       
    }
}
