node('docker') {
    def remote = [:]
    remote.name = 'devapp'
    remote.host = '10.100.65.171'
    remote.user = 'jenkins'
    remote.password = 'Jenkins*CICDDev123'
    remote.allowAnyHosts = true

    stage ('Checkout') {
        checkout scm
    }

    stage ('Build') {
        echo "${currentBuild.number}"
        sh "docker build -t archetype:${currentBuild.number} -f Dockerfile ."
        sh "docker tag archetype:${currentBuild.number} registry-cicd.kerkuz.com/archetype:${currentBuild.number}"
        sh "docker login -u user -p password registry-cicd.kerkuz.com"
        sh "docker push registry-cicd.kerkuz.com/archetype:${currentBuild.number}"
    }

    stage ('Deploy') {
        sshPut remote: remote, from: "docker-compose-archetype.yml", into: "/tmp"
        sshCommand remote: remote, command: "docker login -u user -p password registry-cicd.kerkuz.com"
        sshCommand remote: remote, command: "docker pull registry-cicd.kerkuz.com/archetype:${currentBuild.number}"
        sshCommand remote: remote, command: "export TAG=${currentBuild.number} && docker stack deploy -c /tmp/docker-compose-archetype.yml archetype"
    }   
}
