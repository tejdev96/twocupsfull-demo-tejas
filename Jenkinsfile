/*
File: Jenkinsfile
Author: Twocupsfull
*/

def getRepoName() {
    return scm.getUserRemoteConfigs()[0].getUrl().tokenize('/').last().split("\\.")[0]
}

pipeline {
    agent any
    environment {
        CI = true
        PYTHON_VERSION = "3.10"
        REPOSITORY_NAME = getRepoName()
        PROJECT_NAME = sh(returnStdout: true, script: "echo ${REPOSITORY_NAME}-${BRANCH_NAME}").trim()
        PROJECT_VERSION = sh(returnStdout: true, script: "jq -r .version version.json").trim()
        ARTIFACT = "${REPOSITORY_NAME}-${BRANCH_NAME}-${PROJECT_VERSION}-${BUILD_NUMBER}.tar.gz"
        JFROG_CLI_BUILD_NAME = "${PROJECT_NAME}"
        JFROG_CLI_BUILD_NUMBER = "${BUILD_NUMBER}"
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '7', artifactNumToKeepStr: '', daysToKeepStr: '7', numToKeepStr: '')
    }
    tools {
        jfrog 'jfrog-cli'
    }
    stages {
        stage('Sanity Check') {
            parallel {
                stage('Check Python3'){
                    steps {
                        sh "python${PYTHON_VERSION} --version"
                    }
                }
                stage('Check Virtualenv') {
                    steps {
                        sh 'virtualenv --version'
                    }
                }
                stage('Check Java') {
                    steps {
                        sh 'java -version'
                    }
                }
                stage('Check jq') {
                    steps {
                        sh 'jq --version'
                    }
                }
                stage('JFrog CLI') {
                    steps {
                        jf '-v'
                        jf 'c show'
                    }
                }
            }
        }
        stage('Policy Check') {
            steps {
                script {
                    scannerHome = tool 'twocupsfull-sonar-scanner'
                }
                withSonarQubeEnv('twocupsfull-sonarqube-server') {
                    sh label: 'Scan',
                    script: """
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey="${PROJECT_NAME}" \
                        -Dsonar.projectName="${PROJECT_NAME}" \
                        -Dsonar.projectVersion="${PROJECT_VERSION}" \
                        -Dsonar.python.version="${PYTHON_VERSION}" \
                        -Dsonar.sources=./app
                    """
                }
            }
        }
        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage('Initialize') {
            steps {
                script {
                    sh label: 'Create virtualenvironment',
                    script: """
                        virtualenv --always-copy -p python${PYTHON_VERSION} venv
                    """
                }
                script {
                    sh label: 'Activate virtualenvironment',
                    script: """
                        . ./venv/bin/activate
                        python3 -m pip install --upgrade pip
                        python3 -m pip install -r requirements.txt
                        deactivate
                    """
                }
            }
        }
        stage('Build') {
            steps {
                dir('app') {
                    script {
                        sh label: 'Build HTML',
                        script: """
                            . ./../venv/bin/activate
                            make clean html
                            deactivate
                        """
                    }
                }
            }
        }
        stage('Create Artifacts') {
            steps {
                script {
                    sh label: 'Create tarball',
                    script: """
                        if [ -d "${WORKSPACE}/app/build/html" ]; then
                            cd "${WORKSPACE}/app/build/html"
                            tar -vczf "${WORKSPACE}/${ARTIFACT}" *
                        else
                            echo "Build html directory doesn't exists!"
                            exit 1
                        fi
                    """
                }
            }
        }
        stage('Archive tarball') {
            steps {
                script {
                    archiveArtifacts artifacts: '*.tar.gz',
                    allowEmptyArchive: false,
                    fingerprint: true,
                    followSymlinks: false,
                    onlyIfSuccessful: true
                }
            }
        }
        stage('Upload to Artifactory') {
            when {
                branch 'main'
            }
            steps {
                jf 'rt u ${ARTIFACT} twocupsfull-test/'
                jf 'rt bp'
            }
        }
    }
    post {
        success {
            // Send notification
            script {
                sh label: 'Success',
                script: """
                    echo 'SUCCESS!'
                """
            }
        }
        failure {
            // Send notification
            script {
                sh label: 'Failed',
                script: """
                    echo 'FAIL!'
                """
            }
        }
    }
}
