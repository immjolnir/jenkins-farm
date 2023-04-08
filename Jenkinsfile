// https://www.jenkins.io/doc/book/pipeline/syntax/#parallel

def Greet(name) {
	echo "Hello ${name}."
}

def mul(a, b)
{
   def mul=a*b
   return mul
}

pipeline {
    agent {
      label 'master'
    }
    parameters {
        string(name: 'x1', defaultValue: 'default', description: 'This is a parameter')
    }
    options {
        // https://stackoverflow.com/questions/47039924/jenkins-pipeline-enable-timestamps-in-build-log-console
        // https://linuxhint.com/timestamps-jenkins/
        timestamps()
        parallelsAlwaysFailFast()
    }
    stages {
        stage('Non-Parallel Stage') {
            steps {
                echo 'This stage will be executed first.'
                script { // Call API of shared libraries
                    basename = getDirName("/var/jenkins_home/init.groovy.d/executors.groovy")
                    echo "BASE DIR: ${basename}"
                    log.info "hello ${basename}"
                }
            }
        }
        stage('Parallel Stage (main only)') {
            when {
                branch 'main'
            }
            parallel {
                stage('Branch A') {
                    steps {
                        Greet("Branch A")
                        script {
                            def a = 2
                            def b = 3
                            def res = mul(a, b)
                            echo "${a} * ${b} equals ${res}"
                        }
                    }
                }
                stage('Branch B') {
                    steps {
                        Greet("Branch B")
                        sleep(time: 2, unit: 'MINUTES')
                        script {
                            // https://groovy-lang.org/closures.html
                            def closureWithOneArg = { str -> str.toUpperCase() }
                            def res = closureWithOneArg(params.x1)

                            echo "closureWithOneArg: ${res}"
                        }
                    }
                }
                stage('Branch C') {
                    stages {
                        stage('Nested 1') {
                            steps {
                                Greet("Branch B: 1")
                                echo "In stage Nested 1 within Branch C"
                            }
                        }
                        stage('Nested 2') {
                            steps {
                                Greet("Branch B: 2")
                                echo "In stage Nested 2 within Branch C"
                            }
                        }
                    }
                }
            }
        }
        stage('Reduce Stage') {
            steps {
                echo 'This stage will be executed last.'
            }
        }
    }
    post {
      always {
        // One or more steps need to be included within each condition's block.
        echo "always"
      }
      aborted {
        // One or more steps need to be included within each condition's block.
        echo "aborted"
      }
      success {
        // One or more steps need to be included within each condition's block.
        echo "success"
      }
      failure {
        // One or more steps need to be included within each condition's block.
        echo "failure"
      }
      fixed {
        // One or more steps need to be included within each condition's block.
        echo "fixed"
      }
    }
}
