def label = "ImageBuildPod-${UUID.randomUUID().toString()}"
podTemplate(
        label: label,
        containers: [
                containerTemplate(name: 'opencontrol',
                        image: 'opencontrolorg/compliance-masonry',
                        ttyEnabled: true,
                        command: 'cat',
                        privileged: true),
                containerTemplate(name: 'beeronbeard',
                        image: 'beeronbeard/docker-gitbook-pdf',
                        args: '-e PDF_NAME=fred.pdf',
                        ttyEnabled: true,
                        command: 'cat',
                        privileged: true)
        ])
        {
            node(label) {
                stage('Clean Workspace') {
                    cleanWs()
                }
                stage("checkout") {
                    checkout(
                            [
                                    $class                           : 'GitSCM',
                                    branches                         : scm.branches,
                                    doGenerateSubmoduleConfigurations: scm.doGenerateSubmoduleConfigurations,
                                    extensions                       : scm.extensions,
                                    submoduleCfg                     : [],
                                    userRemoteConfigs                : scm.userRemoteConfigs
                            ]
                    )
                }
                stage('Install Packages') {
                    container('opencontrol') {
                        sh 'get'
                        sh 'docs gitbook FredRAMP-low'
                    }
                    container('beeronbeard') {
                        sh 'cd /book && gitbook install && gitbook pdf /book /pdf/$PDF_NAME'
                        archiveArtifacts artifacts: '**/*'
                    }
                }
            }
        }
