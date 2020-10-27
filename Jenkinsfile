def label = "ImageBuildPod-${UUID.randomUUID().toString()}"
podTemplate(
       label: label,
       containers: [
           containerTemplate(name: 'compliance-masonry',
                       image: 'opencontrolorg/compliance-masonry',
                       ttyEnabled: true,
                       command: 'cat',
                       workingDir:'/opencontrol',
                       privileged: true)
           ],
           volumes: [ hostPathVolume(hostPath: '/home/jenkins/agent/workspace/compliance-masonry-demo_main', mountPath: '/opencontrol') ])
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
            container('compliance-masonry') {
                sh(
                    script: "compliance-masonry get",
                    returnStdout: true
                )
            }
        }
    }
}
