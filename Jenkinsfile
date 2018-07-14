pipeline {

    environment {
        docker_image_name = "python3-unittests"
    }

    agent {
        dockerfile {
            filename 'Dockerfile.build'
            dir '.'
            label env.docker_image_name
        }
    }

    stages {

        stage('静的解析') {
            steps {
                parallel(
                    'Pep8': {
                        dir('.') {
                            script {
                                sh 'flake8 . --select=E101,E113,E125,E129,E304,E7,F4,F8,N8 --max-line-length=120 || true'
                            }
                            step([
                                $class: 'WarningsPublisher',
                                consoleParsers: [
                                    [parserName: 'Pep8']
                                ]
                            ])
                        }
                    }
                )
            }
        }

        stage('ユニットテスト') {
            steps {
                script {
                    dir('.') {
                        sh 'nosetests -v --with-xunit --with-coverage --cover-inclusive --cover-erase --cover-tests --cover-branches  --cover-xml --cover-package=functional_tests,noseselecttests || true'
                    }
                }
                step([
                    $class: 'JUnitResultArchiver',
                    testResults: 'nosetests.xml'
                ])
                step([
                    $class: 'CoberturaPublisher',
                    autoUpdateHealth: false,
                    autoUpdateStability: false,
                    coberturaReportFile: 'coverage.xml',
                    failUnhealthy: false,
                    failUnstable: false,
                    maxNumberOfBuilds: 0,
                    onlyStable: false,
                    zoomCoverageChart: false
                ])
                archiveArtifacts 'nosetests.xml'
                archiveArtifacts 'coverage.xml'
            }
        }

    }
    // post {
    //     always {
    //         script {
    //             sh "docker rmi ${env.docker_image_name}"
    //         }
    //     }
    // }
}
