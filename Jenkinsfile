

pipeline {
    agent {
        docker { image 'thomas:ros-kinetic-thomas-base' }
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                sh 'mkdir .src && mv * .src && mv .src src && cd src'
                sh 'apt-get update && apt-get install -y ros-kinetic-image-exposure-msgs ros-kinetic-wfov-camera-msgs'
                sh ". /opt/ros/kinetic/setup.sh && catkin_init_workspace src && catkin_make -j4"
            }
        }
        stage('Pack') {
            steps {
                echo 'Packing...'
            }
        }
        stage('Dockerize') {
            steps {
                echo 'Dockerizing...'
            }
        }
    }
}

