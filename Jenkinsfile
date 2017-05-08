podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'docker', image: 'docker:latest', ttyEnabled: true, command: 'cat')],
    volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]) {

  node('mypod') {
      stage('Build and Push'){
          git url: 'https://github.com/pblaas/jenkinsphp.git'
          container('docker'){
            stage ('Build Docker image'){
              sh 'which docker; docker version'
              def imageName = "pblaas/jenkinsphp:${env.BUILD_TAG}"
              sh "docker build -t ${imageName}  ."
              def img= docker.image(imageName)

              stage ('Push Docker image'){
                docker.withRegistry("https://registry.hub.docker.com", "docker-registry") {
                  img.push()
                }
              }
            }
          }
      }
    }
  }
node{
  stage('Deploy to cluster'){
    //Set Kubernetes config
    sh("hostname && cat /etc/issue")
    sh("wget https://storage.googleapis.com/kubernetes-release/release/v1.6.1/bin/linux/amd64/kubectl && chmod +x kubectl")
    sh("./kubectl config set-credentials jenkins-build --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token`")
    sh("./kubectl config set-cluster internal1 --server=https://kubernetes --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt")
    sh("./kubectl config set-context default --user=jenkins-build --namespace=`cat /var/run/secrets/kubernetes.io/serviceaccount/namespace`  --cluster=internal1")
    sh("./kubectl config use-context default")
    sh("echo img.id: ${env.BUILD_TAG}")
    //sh("kubectl rolling-update jenkinsphp --image=${img.id} --update-period=1s")
    sh("./kubectl set image deployment jenkinsphp-deployment jenkinsphp=pblaas/jenkinsphp:${env.BUILD_TAG} --update-period=1s")
  }
}
