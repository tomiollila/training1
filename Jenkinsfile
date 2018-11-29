pipeline {
  agent any
  stages {
    stage('Prepare') {
      steps {
        checkout([$class: 'GitSCM',
                                                            branches: [[name: "origin/${BRANCH_PATTERN}"]],
                                                            doGenerateSubmoduleConfigurations: false,
                                                            extensions: [[$class: 'LocalBranch']],
                                                            submoduleCfg: [],
                                                            userRemoteConfigs: [[
                                                                          url: 'https://github.com/tomiollila/training1.git']]])
          sh 'curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl'
          sh 'chmod +x ./kubectl && mv kubectl /usr/local/sbin'
          git(url: 'https://github.com/tomiollila/training1.git', branch: 'master')
        }
      }
      stage('Integration') {
        when {
          expression {
            GIT_BRANCH = 'origin/' + sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
            return GIT_BRANCH == 'origin/master' || params.FORCE_FULL_BUILD
          }

        }
        steps {
          withKubeConfig(credentialsId: 'jenkins-deploy1', serverUrl: 'https://kubernetes.default') {
            sh '''kubectl apply -f /home/jenkins/workspace/training1_master/deploy/nodejs.yaml --namespace=castorlabsdev
            sh 'kubectl apply -f /home/jenkins/workspace/training1_master/deploy/nginx-reverseproxy.yaml --namespace=castorlabsdev'
          }

        }
      }
      stage('Build Skipped') {
        when {
          expression {
            GIT_BRANCH = 'origin/' + sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
            return !(GIT_BRANCH == 'origin/master' || params.FORCE_FULL_BUILD)
          }

        }
        steps {
          echo 'Skipped full build.'
        }
      }
    }
    parameters {
      string(defaultValue: '*', description: '', name: 'BRANCH_PATTERN')
      booleanParam(defaultValue: false, description: '', name: 'FORCE_FULL_BUILD')
    }
  }
