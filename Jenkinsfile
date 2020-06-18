pipeline {

    environment {
        registry = "docker-registry.leyton.com:5000"
    }

    agent any

    stages {

        stage('File valuation') {
            steps {
                script {
                    sh '''
                    printenv | grep VERSION
                    sed -i "s|<VERSION_BACK>|${VERSION_BACK}|" docker-compose.yml
                    sed -i "s|<VERSION_FRONT>|${VERSION_FRONT}|" docker-compose.yml

                    sed -i "s|<MYSQL_ROOT_PASSWORD>|${SPRING_DATASOURCE_PASSWORD}|" docker-compose.yml
                    sed -i "s|<SPRING_DATASOURCE_PASSWORD>|${SPRING_DATASOURCE_PASSWORD}|" docker-compose.yml
                    sed -i "s|<SPRING_JPA_HIBERNATE_DDL>|${SPRING_JPA_HIBERNATE_DDL}|" docker-compose.yml
                    
                    sed -i "s|<HOST>|${HOST}|" docker-compose.yml
                    sed -i "s|<REGISTRY>|${registry}|" docker-compose.yml
                    '''
                }
            }
        }

        stage ('Deploy'){
            steps {
                sh '''
                ssh -vv jenkins@${HOST} sudo mkdir -p /var/opt/dashboard/
                scp -p docker-compose.yml jenkins@${HOST}:${HOME}
                ssh jenkins@${HOST} sudo cp ${HOME}/docker-compose.yml /var/opt/dashboard/
                ssh jenkins@${HOST} sudo rm -f ${HOME}/docker-compose.yml
                ssh jenkins@${HOST} sudo ls -ltr /var/opt/dashboard
                ssh jenkins@${HOST} sudo docker-compose -f /var/opt/dashboard/docker-compose.yml up -d
                ssh jenkins@${HOST} sudo docker volume ls
                ssh jenkins@${HOST} sudo docker ps
                '''
            }
        }

    }

    post {
        always {
            echo 'One way or another, I have finished'
            deleteDir() /* clean up our workspace */
        }
        success {
            echo 'I succeeeded!'
        }
        unstable {
            echo 'I am unstable :/'
        }
        failure {
            echo 'I failed :('
        }


    }

}
