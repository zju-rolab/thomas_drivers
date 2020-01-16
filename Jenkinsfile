

def build_badge = addEmbeddableBadgeConfiguration(id: "build", subject: "Build")

pipeline {
    agent {
        docker { image 'thomas:ros-kinetic-thomas-base' }
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                    script {
                    try {
                        sh 'mkdir .src && mv * .src && mv .src src && cd src'
                        sh 'apt-get update && apt-get install -y ros-kinetic-image-exposure-msgs ros-kinetic-wfov-camera-msgs'
                        sh ". /opt/ros/kinetic/setup.sh && catkin_init_workspace src && catkin_make -j4"
                    } catch (exc) {
                        build_badge.setStatus('failing')
                        throw exc
                    }
                }
            }
        }
        stage('Pack') {
            when {
                branch 'master'  
            }
            steps {
                echo 'Packing...'
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
        stage('Finallize') {
            steps {
                echo 'Finalizing...'
                script {
                    build_badge.setStatus('passing')
                }
            }
        }
    }
}
