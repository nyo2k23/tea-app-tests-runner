pipeline{

    agent any

    parameters {
        choice choices: ['chrome', 'firefox'], description: 'Select the browser', name: 'BROWSER'
    }

    stages{

        environment{
            SE_EVENT_BUS_HOST=${TEA_SELENIUM_SE_HUB}
            SE_EVENT_BUS_PUBLISH_PORT=${TEA_SELENIUM_SE_PUB_PORT}
            SE_EVENT_BUS_SUBSCRIBE_PORT=${TEA_SELENIUM_SE_SUB_PORT}
            SE_NODE_OVERRIDE_MAX_SESSIONS=${TEA_SELENIUM_SE_OVERRIDE_MAX_SESSIONS}
            SE_NODE_MAX_SESSIONS=${TEA_SELENIUM_SE_NODE_SESSIONS}
            UI_URL=${TEA_SELENIUM_UI_URL}
        }
        stage('Start Grid'){
            steps{
                sh "docker-compose -f grid.yaml up --scale ${params.BROWSER}=2 -d"
            }
        }

        stage('Run Test'){
            steps{
                sh "docker-compose -f test-suites.yaml up --pull=always"
                script {
                    if(fileExists('output/user-login/testng-failed.xml')
                    // || fileExists('output/another-test-suite/testng-failed.xml')
                    ){
                        error('failed tests found')
                    }
                }
            }
        }

    }

    post {
        always {
            sh "docker-compose -f grid.yaml down"
            sh "docker-compose -f test-suites.yaml down"
            archiveArtifacts artifacts: 'output/user-login/emailable-report.html', followSymlinks: false
        }
    }

}