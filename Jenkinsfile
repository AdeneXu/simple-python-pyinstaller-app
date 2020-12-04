pipeline{
    agent none
    options {
        skipStagesAfterUnstable()
    }
    stages{
        stage('Build'){
            agent {
                docker{
                    image 'python:2-alpine'
                }
            }
            steps{
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test'){
            agent{
                docker {
                    image 'qnib/pytest'
                }
            }
            steps{
                sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            post{
                always{
                    junit 'test-reports/results.xml'
                }
            }
        }
//         stage('Deliver'){
//             agent{
//                 docker{
//                     image 'cdrx/pyinstaller-linux:python2'
//                 }
//             }
//             steps{
//                 sh 'pyinstaller --onefile sources/add2vals.py'
//             }
//             post{
//                 success{
//                     archiveArtifacts 'dist/add2vals'
//                 }
//             }
//         }

          stage('Deliver') {
            agent any
            environment {
                VOLUME = 'fb8ce7f715477876311001c19dc6191964b3ad0e00404a62ef698969aaf28a29'
                DIR = '$(pwd)/sources/'
                IMAGE = 'cdrx/pyinstaller-linux:python2'
            }
            steps {
                dir(path: env.BUILD_ID) {
                    unstash(name: 'compiled-results')
                    sh "docker run --rm -v '$(pwd):/src/' ${IMAGE} 'pyinstaller -F add2vals.py' "
                }
            }
            post {
                success {
                    archiveArtifacts "dist/add2vals"
//                     sh "docker run --rm ${IMAGE} 'rm -rf build dist'"
                }
            }
        }
    }
}