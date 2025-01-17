node() {
    try {
        stage("Get repositories") {
            script{
                def pyrdf_docker
                def worker_docker
                def currenttime
                git 'http://localhost:3000/Vetch/RunWIthSecond'
            }
        }
        stage("Build base"){
            script{
                docker.build("root_utils", "--network='host' BaseROOTcompile")
            }
        }

        stage("build images"){
            parallel(
                "command": {
                    stage("Build command centre") {
                        script{
                            pyrdf_docker = docker.build("pyrdf_terraform", "--network='host' commandRING")
                        }
                    }
                    stage("Run image") {
                        pyrdf_docker.inside("--network='host' -v /var/run/docker.sock:/var/run/docker.sock -v /mnt/dav:/mnt/dav")
                        {
                            // sh '. /cern_root/root/bin/thisroot.sh && python2 /cern_root/root/PyRDF/introduction.py'
                            // sh 'cd /terraform && terraform init &&  terraform apply -auto-approve && terraform destroy -auto-approve'
                            sh """
                            mkdir -p /mnt/dav/AWS_ROOT
                            cp /*.pdf /mnt/dav/AWS_ROOT
                            cd /mnt/cern_root
                            zip -r /mnt/dav/AWS_ROOT/aws_root.zip chroot root_install
                            """
                        }
                    }
                },
                "worker": {
                    stage("Build worker") {
                        script{
                            worker_docker = docker.build("worker_image", "--network='host' WorkerNode")
                        }
                    }
                    stage("Run worker") {
                        worker_docker.inside{
                            sh '. /cern_root/root/bin/thisroot.sh && root -b'
                        }
                    }
                }
            )
        }
        stage("finish up"){
            pyrdf_docker.inside("--network='host'")
            {
                sh '. /cern_root/root/bin/thisroot.sh && python2 /cern_root/root/PyRDF/introduction.py'
            }

            def msg = "`${env.JOB_NAME}#${env.BUILD_NUMBER}`:\n${env.BUILD_URL} duration: ${currentBuild.durationString.split('and')[0]}"
            mattermostSend color: 'good', message: msg, text: 'optional for @here mentions and searchable text'
        }
    }
    catch (e) {
        def msg = "`FAILED`\n${env.JOB_NAME}: #${env.BUILD_NUMBER}:\n${env.BUILD_URL} ${e} duration: ${currentBuild.durationString.split('and')[0]}"
        mattermostSend color: 'good', message: msg, text: 'optional for @here mentions and searchable text'
    }
}