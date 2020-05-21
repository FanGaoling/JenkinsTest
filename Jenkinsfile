pipeline {
  agent any
  stages {
    stage('script - stage') {
      steps {
        script {
          def browsers = ['chrome', 'firefox']
          for (int i = 0; i < browsers.size(); ++i) {
            tools.PrintMes("Testing the ${browsers[i]} browser",'red')
          }
        }

      }
    }

    stage('sharelib - stage') {
      when {
        environment name: 'variable', value: 'build'
      }
      options {
        timeout(time: 1, unit: 'HOURS')
      }
      steps {
        script {
          tools.PrintMes("获取代码",'green')
          // tools.PrintMes(hello(),'green')
          // tools.PrintMes(hello('Joe'),'green')
          hello()
          hello('Joe')
        }

      }
    }

    stage('printParameter - Staging') {
      steps {
        echo "参数：${params}"
        echo "用户名1 ${deploy_username}"
        echo "用户名2 ${params.deploy_username}"
        echo "用户名3 "+params.deploy_username
        echo "用户名4 "+deploy_username
        echo "printParameter stage: 部署机器的名称 : ${params.deploy_hostname} ..."
        echo "DeprintParameterploy stage: 部署机器的用户名 : ${params.deploy_username} ..."
        echo "printParameter stage: 部署连接的密码 : ${params.deploy_password} ..."
        echo "printParameter stage: Release Note的信息为 : ${params.release_note} ..."
        echo "printParameter stage: 选中的构建Module为 : ${params.modulename} ..."
        echo "变量值：${variable} (默认值zhangsan)"
        script {
          tools.PrintMes("变量值：${variable} (zhangsan)",'green')
        }

      }
    }

    stage('setVarVal - Staging') {
      steps {
        echo "用户名5 ${params.deploy_username}"
        echo "Test stage: 是否执行自动化测试: ${params.test_skip_flag} ..."
        echo 'Testing'
        script {
          sh "chmod +x ./shfolder/first.sh"
          sh "chmod +x ./shfolder/second.sh"
          sh "chmod +x ./shfolder/three.sh"

          tools.PrintMes("变量值：${variable} (zhangsan)",'green')

          variable=sh(script: "./shfolder/first.sh ${variable}", returnStdout: true).trim()
          tools.PrintMes("变量值：${variable} (lisi2)",'green')

          // sh '/home/app/jenkins/testreturn.sh > commandResult'
          sh "${jenkinsUrl}testreturn.sh > commandResult"
          variable=readFile('commandResult').trim()
          tools.PrintMes("变量值：${variable} (lisi)",'green')

          variable=sh(script: "/home/app/jenkins/testreturn2.sh", returnStdout: true).trim()
        }

      }
    }

    stage('verifyVariable - Staging') {
      steps {
        script {
          tools.PrintMes("变量值：${variable} (wangwu)",'green')
        }

      }
    }

    stage('Parallel - Staging') {
      failFast true
      parallel {
        stage('Parallel - A') {
          steps {
            echo '-- on Parallel - A'
          }
        }

        stage('Parallel - B') {
          steps {
            echo '-- on Parallel - B'
          }
        }

        stage('Parallel - C') {
          steps {
            echo 'Parallel C'
          }
        }

      }
    }

    stage('RetryAndTimeout - Staging') {
      post {
        always {
          echo 'post in stage'
        }

      }
      steps {
        retry(count: 3)
        timeout(time: 3, unit: 'MINUTES')
      }
    }

  }
  environment {
    CC = 'clang'
  }
  post {
    always {
      echo '-- This will always run'
    }

    changed {
      echo '-- This will run only if the state of the Pipeline has changed'
      echo '-- For example, if the Pipeline was previously failing but is now successful'
    }

    failure {
      echo '-- This will run only if failed'
    }

    success {
      echo '-- This will run only if successful'
      mail(to: '13111002493@163.com', subject: "successed Pipeline: ${currentBuild.fullDisplayName}", body: "Something is right with ${env.BUILD_URL}")
    }

    unstable {
      echo '-- This will run only if the run was marked as unstable'
    }

    aborted {
      echo '-- This will run only if aborted'
    }

  }
  options {
    buildDiscarder(logRotator(numToKeepStr: '1'))
    disableConcurrentBuilds()
    retry(3)
    skipStagesAfterUnstable()
    timeout(time: 1, unit: 'HOURS')
  }
  parameters {
    choice(description: '你需要选择哪个模块进行构建 ?', name: 'modulename', choices: ['Module1', 'Module2', 'Module3'])
    string(description: '当前所属stage ?', name: 'variable', defaultValue: 'zhangsan')
    string(description: '你需要在哪台机器上进行部署 ?', name: 'deploy_hostname', defaultValue: 'host131')
    string(description: '部署机器连接时需要用到的用户名是什么 ?', name: 'deploy_username', defaultValue: 'admin')
    text(name: 'release_note', defaultValue: '''Release Note 信息如下所示: 
             Bug-Fixed: 
             Feature-Added: ''', description: 'Release Note的详细信息是什么 ?')
    booleanParam(name: 'test_skip_flag', defaultValue: true, description: '你需要在部署之前执行自动化测试么 ?')
    password(name: 'deploy_password', defaultValue: 'liumiaocn', description: '部署机器连接时需要用到的密码信息是什么 ')
    file(name: 'deploy_property_file', description: '你需要输入的部署环境的设定文件是什么 ?')
  }
}