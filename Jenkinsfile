def label = "ImageBuildPod-${UUID.randomUUID().toString()}"
podTemplate(
        label: label,
        containers: [
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
                                script: "/go/bin/compliance-masonry get",
                                returnStdout: true
                        ).trim()
                        sh(
                                script: "/go/bin/compliance-masonry docs gitbook FredRAMP-low",
                                returnStdout: true
                        ).trim()
                    }
                    container('beeronbeard') {
                        sh(
                                script: "gitbook install && gitbook pdf ./exports fred.pdf",
                                returnStdout: true
                        ).trim()
                        archiveArtifacts artifacts: 'fred.pdf'
                    }
                }
            }
        }
