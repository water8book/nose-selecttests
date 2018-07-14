pipeline {

    environment {
        docker_image_name = "python3-unittests"
    }

    agent {
        dockerfile {
            filename '../workspace@script/Dockerfile.build'
            dir '../workspace@script'
            label env.docker_image_name
        }
    }

    stages {

        stage('静的解析') {
            steps {
                parallel(
                    'Pep8': {
                        dir('serverless') {
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
                        sh 'nosetests -v --with-xunit --with-coverage --cover-inclusive --cover-branches --cover-xml -w test || true'
                    }
                }
                step([
                    $class: 'JUnitResultArchiver',
                    testResults: 'serverless/nosetests.xml'
                ])
                step([
                    $class: 'CoberturaPublisher',
                    coberturaReportFile: 'serverless/test/coverage.xml'
                ])
                archiveArtifacts 'serverless/nosetests.xml'
                archiveArtifacts 'serverless/test/coverage.xml'
            }
        }

    }
}
