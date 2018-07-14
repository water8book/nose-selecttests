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
                    dir('functional_tests') {
                        sh 'nosetests -v --with-xunit --with-coverage --cover-tests  --cover-inclusive --cover-branches --cover-xml -w --cover-package=noseselecttests noseselecttests/tests.py || true'
                        
                    }
                }
                step([
                    $class: 'JUnitResultArchiver',
                    testResults: 'functional_tests/nosetests.xml'
                ])
                step([
                    $class: 'CoberturaPublisher',
                    coberturaReportFile: 'noseselecttest/coverage.xml'
                ])
                archiveArtifacts 'functional_tests/nosetests.xml'
                archiveArtifacts 'noseselecttest/coverage.xml'
            }
        }

    }
}
