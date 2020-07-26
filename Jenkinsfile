//git凭证ID
def git_auth = "5f62fc97-9b15-43d1-b03e-9faf75905bbb"
//git的url地址
def git_url = "https://github.com/Mr-zhango/tensquare_back_jenkins.git"
//镜像的版本号
def tag = "latest"
//Harbor的url地址
def harbor_url = "192.168.1.129:85"
//镜像库项目名称
def harbor_project = "tensquare"
//Harbor的登录凭证ID
def harbor_auth = "0f032004-5917-417b-ab90-0578060fd354"

node {
   //获取当前选择的项目名称
   def selectedProjectNames = "${project_name}".split(",")
   //获取当前选择的服务器名称
   def selectedServers = "${publish_server}".split(",")

   stage('拉取代码') {
      checkout([$class: 'GitSCM', branches: [[name: "*/${branch}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: "${git_auth}", url: "${git_url}"]]])
   }
}