node {
    stage 'Retrieve Code'
    git url: 'https://github.com/pblaas/jenkinsphp.git'

    stage 'Build Docker image'
    def image = docker.build('pblaas/simplephp:snapshot', '.')

    stage 'Push Docker image'
    docker.withRegistry("https://registry.hub.docker.com", "docker-registry") {
        image.push()
    }
}
