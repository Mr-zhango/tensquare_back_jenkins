//git凭证ID
def git_auth = "b632ed00-fc81-43c8-a746-5aa0673b2658"
//git的url地址
def git_url = "git@192.168.66.100:itheima_group/tensquare_back.git"
//镜像的版本号
def tag = "latest"
//Harbor的url地址
def harbor_url = "192.168.66.102:85"
//镜像库项目名称
def harbor_project = "tensquare"
//Harbor的登录凭证ID
def harbor_auth = "833d1a75-f3db-4aec-9cc4-75a77e423163"

node {
   stage('拉取代码') {
      checkout([$class: 'GitSCM', branches: [[name: "*/${branch}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: "${git_auth}", url: "${git_url}"]]])
   }
   stage('代码审查') {
        //定义当前Jenkins的SonarQubeScanner工具
        def scannerHome = tool 'sonar-scanner'
        //引用当前JenkinsSonarQube环境
        withSonarQubeEnv('sonarqube') {
             sh """
                     cd ${project_name}
                     ${scannerHome}/bin/sonar-scanner
             """
        }

   }
   stage('编译，安装公共子工程') {
      sh "mvn -f tensquare_common clean install"
   }
   stage('编译，打包微服务工程，上传镜像，部署应用') {

         sh "mvn -f ${project_name} clean package dockerfile:build"

         //定义镜像名称
         def imageName = "${project_name}:${tag}"

         //对镜像打上标签
         sh "docker tag ${imageName} ${harbor_url}/${harbor_project}/${imageName}"

        //把镜像推送到Harbor
        withCredentials([usernamePassword(credentialsId: "${harbor_auth}", passwordVariable: 'password', usernameVariable: 'username')]) {

            //登录到Harbor
            sh "docker login -u ${username} -p ${password} ${harbor_url}"

            //镜像上传
            sh "docker push ${harbor_url}/${harbor_project}/${imageName}"

            sh "echo 镜像上传成功"
        }

        //把本地镜像删除
        sh "docker rmi ${imageName}"
        sh "docker rmi ${harbor_url}/${harbor_project}/${imageName}"

        //部署应用
        sshPublisher(publishers: [sshPublisherDesc(configName: 'master_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '/opt/jenkins_shell/deploy.sh $harbor_url $harbor_project $project_name $tag $port', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

   }
}