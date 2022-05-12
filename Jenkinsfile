// def getWebsiteVersion(version) {
//     if (version == '') {
//         return '56'
//     } else {
//         return version
//     }
// }

pipeline {
    agent any
    
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }
    
    environment {
        // BUILD_TYPE = 56
        REPO_NAME = "dashweb-${BUILD_TYPE}"
        // BUILD_TYPE = getWebsiteVersion(params.BUILD_TYPE)
    }

    parameters {
        string(name: 'BUILD_NAME', defaultValue: '', description: 'Commit id to deploy')
        // string(name: 'BUILD_TYPE', defaultValue: '56', description: 'Current Website version')
        choice choices: ['56', '74'], description: '''Which type of container to build.''', name: 'BUILD_TYPE'
        booleanParam name: 'DEPLOY_TO_DEV', defaultValue: false, description: 'Deploy to Dev'
    }
    
    stages {
        stage('Deploy') {
            when { expression { DEPLOY_TO_DEV == 'true' }}
            steps {
                script {
                    VERSION = "build-${REPO_NAME}-${BUILD_TYPE}"
                }
                //Clear previous build
                sh "rm -rf target/*.jar"
                sh (label: "Clean workspace",script: "git clean -fdx")
                echo "Version ${params.BUILD_TYPE}"
                echo "Repo name ${REPO_NAME}"
                // Get some code from a GitHub repository
                git 'https://github.com/jglick/simple-maven-project-with-tests.git'

                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                echo "VERSION is ${VERSION}"
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}
