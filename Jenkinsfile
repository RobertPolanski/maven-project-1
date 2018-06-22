pipeline {
    agent any

    parameters {
        string(name: 'tomcat_dev', defaultValue: '18.185.83.95', description: 'Staging Server')
        string(name: 'tomcat_prod', defaultValue: '18.197.103.82', description: 'Production Server')
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Deployments') {
            parallel {
                stage('Deploy to Staging') {
                    steps {
                        sh "rsync -p --chmod=u+rwx,g+rwx,o+rwx \
                                -e 'ssh -i /u1/programme/tomcat-demo.pem' \
                                /home/robert/.jenkins/jobs/FullyAutomated/builds/5/archive/**/target/*.war \
                                ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps"

                    }
                }

                stage("Deploy to Production") {
                    steps {
                        sh "rsync -p --chmod=u+rwx,g+rwx,o+rwx \
                                -e 'ssh -i /u1/programme/tomcat-demo.pem' \
                                /home/robert/.jenkins/jobs/FullyAutomated/builds/5/archive/**/target/*.war \
                                ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps"
                    }
                }
            }
        }
    }
}