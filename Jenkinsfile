podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'docker', image: 'docker:latest', ttyEnabled: true, command: 'cat')],
    volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]) {

  node('mypod') {
      stage('Build and Push'){
          checkout scm
          container('docker'){
            stage ('Build Docker image'){
              sh 'which docker; docker version'
              def imageName = "pblaas/${env.JOB_NAME}:${env.BUILD_TAG}"
              sh "docker build -t ${imageName}  ."
              def img= docker.image(imageName)

              stage ('Push Docker image to registry'){
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
    echo sh(returnStdout: true, script: 'env')
    //sh("hostname && cat /etc/issue")
    sh("wget -q https://storage.googleapis.com/kubernetes-release/release/v1.6.1/bin/linux/amd64/kubectl && chmod +x kubectl")
    sh("./kubectl config set-credentials jenkins-build --token=`cat /var/run/secrets/kubernetes.io/serviceaccount/token`")
    sh("./kubectl config set-cluster internal1 --server=https://10.3.0.1 --certificate-authority=/var/run/secrets/kubernetes.io/serviceaccount/ca.crt")
    sh("./kubectl config set-context default --user=jenkins-build --namespace=`cat /var/run/secrets/kubernetes.io/serviceaccount/namespace`  --cluster=internal1")
    sh("./kubectl config use-context default")
    sh("echo img.id: ${env.BUILD_TAG}")
    //sh("./kubectl version")
    //sh("./kubectl get deployment")
    sh("./kubectl -n default set image deployment ${env.JOB_NAME}-deployment ${env.JOB_NAME}=pblaas/${env.JOB_NAME}:${env.BUILD_TAG}")
  }
}
