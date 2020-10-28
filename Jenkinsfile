def label = "ImageBuildPod-${UUID.randomUUID().toString()}"
podTemplate(
        label: label,
        containers: [
                containerTemplate(name: 'git', image: 'alpine/git', ttyEnabled: true, command: 'cat'),
                containerTemplate(name: 'opencontrol', image: 'opencontrolorg/compliance-masonry', ttyEnabled: true, command: 'cat'),
                containerTemplate(name: 'beeronbeard', image: 'beeronbeard/docker-gitbook-pdf', ttyEnabled: true, command: 'cat')
        ],
        nodeSelector: 'role=workers'
)
        {
            node(label) {
                stage('Clean Workspace'){
                    cleanWs()
                    checkout scm
                }
                stage('Install Packages') {
                    container('opencontrol'){
                        sh(
                                script: "get",
                                returnStdout: true
                        ).trim()
                        sh(
                                script: "docs gitbook FredRAMP-low",
                                returnStdout: true
                        ).trim()
                    }
                    container('beeronbeard') {
                        sh(
                                script: "cd /book && gitbook install && gitbook pdf /book /pdf/fred.pdf",
                                returnStdout: true
                        ).trim()
                        archiveArtifacts artifacts: '**/*'
                    }
                }
            }
        }
