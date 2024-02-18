def vm2=[:]
vm2.name = 'vm2sv'
vm2.host = '192.168.56.101'
vm2.user = 'theerapat'
vm2.port = 22
vm2.password = '625735'
vm2.allowAnyHosts = true

def vm3=[:]
vm3.name = 'vm3sv'
vm3.host = '192.168.56.102'
vm3.user = 'theerapat'
vm3.port = 22
vm3.password = '625735'
vm3.allowAnyHosts = true


pipeline {
    agent any

    stages {
        stage('UnitTest on Vm2') {
            steps {
                echo 'Testing..'
                 script {
                    echo 'git pull'
                    sshCommand(remote: vm2, command: 'cd Jenkins-assignment/ && git pull')
                
                    echo 'Run unit test'
                    sshCommand(remote: vm2, command: 'cd Jenkins-assignment/ && python3 unit_test.py')
                }
            }
        }
        stage('Build on Vm2') {
            steps {
                script {
                    echo 'Building..'
                    sshCommand(remote: vm2, command: "cd Jenkins-assignment/ && echo '625735' | sudo -S docker-compose up -d --build")
                }
            }
        }
        stage('RobotTest on Vm2') {
            steps {
                script {
                    echo 'git pull'
                    sshCommand(remote: vm2, command: 'cd robot-jenkins-assignment/ && git pull')

                    echo 'Testing..'
                    sshCommand(remote: vm2, command: "python3 -m robot robot-jenkins-assignment/test_api.robot")
                }
            }
        }
        stage('Push image to gitlab Registry on Vm2') {
            steps {
                script {
                    echo 'gitlab login & push'
                    sshCommand(remote: vm2, command: "cd Jenkins-assignment/ \
                    && echo '12345678' | sudo -S docker login registry.gitlab.com \
                    && echo 'Jimmymonster' \
                    && echo 'glpat-f4dTxzyDBemNLeNr8MZj' \
                    && echo '12345678' | sudo -S docker build -t registry.gitlab.com/jimmymonster/jenkins-api-unittest . \
                    && echo '12345678' | sudo -S docker push registry.gitlab.com/jimmymonster/jenkins-api-unittest"
                    )

                }
            }
        }
        stage('Deploy on Vm3') {
            steps {
                echo 'gitlab pull and create container'
                sshCommand(remote: vm3, command: "echo '12345678' | sudo -S docker login registry.gitlab.com \
                    && echo 'Jimmymonster' \
                    && echo 'glpat-f4dTxzyDBemNLeNr8MZj' \
                    && echo '12345678' | sudo -S docker pull registry.gitlab.com/jimmymonster/jenkins-api-unittest \
                    && echo '12345678' | sudo -S docker stop api \
                    && echo '12345678' | sudo -S docker rm api \
                    && echo '12345678' | sudo -S docker run -d -p 8001:5000 --name api registry.gitlab.com/jimmymonster/jenkins-api-unittest"
                    )

            }
        }
    }
}