node('jnlp-slave') {
    stage('克隆代码') {
      echo "1.Clone Stage"
      git url: "https://github.com/hbstarjason/jenkins-demo.git"
	  script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
		}	
    }
    stage('静态代码检查') {
      echo "2.Test Stage"
    }
    stage('单元测试') {
      echo "3.Build Stage"
    }
    stage('编译打包') {
      echo "4.Build Stage"
    }
    stage('构建镜像') {
      echo "5.Build Stage"
      sh "docker build -t hbstarjason/jenkins-demo:${build_tag} ."
    }
    stage('上传镜像') {
      echo "6. Deploy Stage"
      withCredentials([usernamePassword(credentialsId: 'dockerHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
        sh "docker push hbstarjason/jenkins-demo:${build_tag}"
       }
	}   
    stage('部署') {
      echo "7. Deploy Stage"
      def userInput = input(
            id: 'userInput',
            message: 'Choose a deploy environment',
            parameters: [
                [
                    $class: 'ChoiceParameterDefinition',
                    choices: "Dev\nQA\nProd",
                    name: 'Env'
                ]
            ]
        )
        echo "This is a deploy step to ${userInput}"
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        if (userInput == "Dev") {
            // deploy dev stuff
        } else if (userInput == "QA"){
            // deploy qa stuff
        } else {
            // deploy prod stuff
        }
        sh "kubectl apply -f k8s.yaml"
    }
}