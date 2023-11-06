pipeline{

    agent any

    parameters {
        choice choices: ['chrome', 'firefox'], description: 'Select the browser', name: 'BROWSER'
    }

    stages{

        
        stage('Start Grid'){
        
            steps{
                sh "docker-compose -f grid.yaml up --scale ${params.BROWSER}=2 -d"
            }
        }

        stage('Run Test'){
            
            steps{
                // sh "export "
                sh "docker-compose -f test-suites.yaml up"
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