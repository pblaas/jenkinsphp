podTemplate(label: 'mypod', containers: [
    containerTemplate(name: 'docker', image: 'docker:latest', ttyEnabled: true, command: 'cat'),
  ]) {

  node('mypod') {
    stage('Build and Push jenkinsphp'){
        git url: 'https://github.com/pblaas/jenkinsphp.git'
        container('docker'){
          stage ('Build Docker image'){
            sh 'which docker; docker version'
            sh 'service docker start'
            def image = docker.build('pblaas/simplephp:snapshot', '.')
          }
          stage ('Push Docker image'){
            docker.withRegistry("https://registry.hub.docker.com", "docker-registry") {
              image.push()
            }
          }
        }

    }
  }
}
