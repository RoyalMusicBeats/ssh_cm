node {
    def app
    def runningContainer

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("ssh/ssh")
    }

    stage('Start image') {
        /* Start the Docker container using the built image */

        runningContainer = docker.image("ssh/ssh").run()
    }

    stage('Multi-Approval to Prod') {
        /* This stage requires multiple manual approvals before deploying to production */

        parallel (
            "Approval 1": {
                input "Approve deployment to production?"
                runningContainer.stop()
            },
            "Approval 2": {
                input "Approve deployment to production?"
            }
        )
    }



    stage('Push image to prod registry') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withRegistry('https://cm-prod-boz-001.tail118e1.ts.net') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}