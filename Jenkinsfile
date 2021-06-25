pipeline {

  agent any

  stages {

    stage('Git Clone') {
      steps {
         script {
          git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/patiwat13/nginx-say.git'
          sh 'pwd'
            }
      }
                       }
    
    stage("Docker build"){
        steps {
                           script {
        sh 'docker version'
        sh 'docker build -t nginx-docker-jenkins .'
        sh 'docker image list'
        sh 'docker tag nginx-docker-jenkins liquid07/nginx-docker-demo:jenkins-nginx'
        sh 'docker images list'
                             
    stage("Docker Login"){
        withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
            sh 'docker login -u liquid07 -p $PASSWORD'
            echo 'DOCKER LOGIN SUCESS..'
                        }
                                  }
                             
      stage("Push Image to Docker Hub"){
        sh 'docker push  liquid07/nginx-docker-demo:jenkins-nginx'
        echo 'Push Sucess!!'
    }
                             
         stage("SSH Into Virtual machine Server") {
        def remote = [:]
        remote.name = 'K8S master'
        remote.host = '192.168.1.100'
        remote.user = 'root'
        remote.password = 'zjkoC]6p'
        remote.allowAnyHosts = true

        stage('Put nginx-deployment.yaml onto k8s master') {
            sshPut remote: remote, from: 'nginx-deployment.yaml', into: '.'

        }
        stage("Login to Rancher"){
      kubeconfig(caCertificate: '''-----BEGIN CERTIFICATE-----
MIIBiDCCAS6gAwIBAgIBADAKBggqhkjOPQQDAjA7MRwwGgYDVQQKExNkeW5hbWlj
bGlzdGVuZXItb3JnMRswGQYDVQQDExJkeW5hbWljbGlzdGVuZXItY2EwHhcNMjEw
MzI0MDUwNzA2WhcNMzEwMzIyMDUwNzA2WjA7MRwwGgYDVQQKExNkeW5hbWljbGlz
dGVuZXItb3JnMRswGQYDVQQDExJkeW5hbWljbGlzdGVuZXItY2EwWTATBgcqhkjO
PQIBBggqhkjOPQMBBwNCAARMO7Ag8armJKKvhRcxCsHp1+oWwLiHoY7krAeOjVca
hzXGQu5zaAQyRvgvE8TElXvTVJV6GuFUpAfMqCcA/k8YoyMwITAOBgNVHQ8BAf8E
BAMCAqQwDwYDVR0TAQH/BAUwAwEB/zAKBggqhkjOPQQDAgNIADBFAiEA6cmHVpNu
1oURR8FBKm46uldAVG8/iqed05X5Ob0VXXQCIDpXcbQUussfetNfmHb65hwc/2tW
P9V/xvyOokeKAvZd
-----END CERTIFICATE-----''', credentialsId: 'Rancher_Login', serverUrl: 'https://203.151.50.20/k8s/clusters/c-bfhk6') {
    // some block
            stage('Deploy Nginx YAML File') {
                  sh 'kubectl apply -f nginx-deployment.yaml'
                  // sh 'kubectl get pod'
                                    }
            } 
                         }
    
         }
    }
  }
  
}
  }
