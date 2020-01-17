

def build_badge = addEmbeddableBadgeConfiguration(id: 'build', subject: 'Build')

pipeline {
    agent any
    
    stages {
        stage('Build') {

            agent {
                docker { image 'thomas:ros-kinetic-thomas-base' }
            }

            steps {
                script {
                    build_badge.setStatus('building')
                    try {
                        sh 'mkdir .src && mv * .src && mv .src src && cd src'
                        sh 'apt-get update && apt-get install -y ros-kinetic-image-exposure-msgs ros-kinetic-wfov-camera-msgs'
                        sh '. /opt/ros/kinetic/setup.sh && catkin_init_workspace src && catkin_make -j4'
                        build_badge.setStatus('passing')
                    } catch (Exception err) {
                        build_badge.setStatus('failing')
                        error 'Build failed'
                    }
                }
            }
        }
        stage('Pack') {

            // when {
            //     branch 'master'  
            // }

            agent {
                docker { image 'thomas:ros-kinetic-thomas-base' }
            }

            steps {
                sh 'version=$(cat src/thomas_drivers/thomas_drivers/package.xml | grep "<version>" | cut -d">" -f2 | cut -d"<" -f1) && mkdir thomas-drivers-${version}'
                sh '. /opt/ros/kinetic/setup.sh && catkin_make install thomas-drivers-*'
            }
        }
        stage('Publish') {
            when {
                branch 'master'  
            }
            steps {
                echo 'Publishing...'
            }
        }
        stage('Dockerize') {
            when {
                branch 'master'  
            }
            steps {
                echo 'Dockerizing...'
            }
        }
    }
}
