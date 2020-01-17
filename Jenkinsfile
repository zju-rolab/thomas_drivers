

def build_badge = addEmbeddableBadgeConfiguration(id: 'build', subject: 'Build')
def pack_badge = addEmbeddableBadgeConfiguration(id: 'build', subject: 'Build')

def runBuild() {
    sh 'mkdir .src && mv * .src && mv .src src && cd src'
    sh '. /opt/ros/kinetic/setup.sh && catkin_init_workspace src && catkin_make -j4'
}

def runPack() {
    sh '''                                                                              \
        VERSION=$(cat src/thomas_drivers/package.xml |                                  \
            grep "<version>" | cut -d">" -f2 | cut -d"<" -f1) &&                        \
        . /opt/ros/kinetic/setup.sh &&                                                  \
        PKG_NAME="thomas-drivers" &&                                                    \
        WORKSPACE="thomas_drivers" &&                                                   \
        PKG_DIR="./${PKG_NAME}-${VERSION}" &&                                           \
        WORKSPACE_DIR="${PKG_DIR}/opt/${WORKSPACE}" &&                                  \
        mkdir -p ${WORKSPACE_DIR} &&                                                    \
        catkin_make install -DCMAKE_INSTALL_PREFIX="${WORKSPACE_DIR}" &&                \
        mkdir ${PKG_DIR}/DEBIAN &&                                                      \
        CONTROL_FILE=${PKG_DIR}/DEBIAN/control &&                                       \
        echo "Package: ${PKG_NAME}"                             >> ${CONTROL_FILE} &&   \
        echo "Version: ${VERSION}"                              >> ${CONTROL_FILE} &&   \
        echo "Section: base"                                    >> ${CONTROL_FILE} &&   \
        echo "Priority: optional"                               >> ${CONTROL_FILE} &&   \
        echo "Architecture: amd64"                              >> ${CONTROL_FILE} &&   \
        echo "Depends: ros-kinetic-desktop-full"                >> ${CONTROL_FILE} &&   \
        echo "Maintainer: Li Tang <litang.cv@email.com>"        >> ${CONTROL_FILE} &&   \
        echo "Description: Some common drivers"                 >> ${CONTROL_FILE} &&   \
        echo " Now includes velodyne, xsens and flir cameras"   >> ${CONTROL_FILE} &&   \
        chmod -R a-s ${PKG_DIR} &&                                                      \
        dpkg-deb --build ${PKG_DIR}                                                     \
       '''
}

pipeline {

    agent { 
        label "master"
    }

    stages {

        stage('Build') {

            agent {
                docker {
                    image 'thomas:ros-kinetic-thomas-base'
                    reuseNode true
                }
            }

            steps {
                script {
                    build_badge.setStatus('building')
                    try {
                        runBuild()
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
                docker {
                    image 'thomas:ros-kinetic-thomas-base'
                    reuseNode true
                }
            }

            steps {
                script {
                    pack_badge.setStatus('building')
                    try {
                        runPack()
                        pack_badge.setStatus('passing')
                    } catch (Exception err) {
                        pack_badge.setStatus('failing')
                        error 'Pack failed'
                    }
                }
            }
        }

        stage('Publish') {

            // when {
            //     branch 'master'  
            // }

            steps {
                sh 'cp thomas-drivers-*.deb /pkgs'
            }
        }

        stage('Dockerize') {

            // when {
            //     branch 'master'  
            // }

            steps {
                echo 'Dockerizing...'
            }
        }
    }
}
