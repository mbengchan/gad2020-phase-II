# LAB: AK8S-02 Working with Cloud Build

## Objectives:

In this lab, you learn how to perform the following tasks:

    - Use Cloud Build to build and push containers

    - Use Container Registry to store and deploy containers

## Steps:

1. Use Cloud Build to build and push containers

    Task 1: Enable needed APIs.

        gcloud services enable cloudbuild.googleapis.com

        gcloud services enable containerregistry.googleapis.com

    Task 2. Building Containers with DockerFile and Cloud Build

        1. Create an empty quickstart.sh file using the nano text editor.

            nano quickstart.sh

        2. Add the following lines in to the quickstart.sh file:

            #!/bin/sh
            echo "Hello, world! The time is $(date)."
        
        3. Save the file and close nano by pressing the CTRL+X key, then press Y and enter.

        4. Create an empty Dockerfile file using the nano text editor.

            nano Dockerfile

        5. Add the following Dockerfile command:

            FROM alpine

            This instructs the build to use the Alpine Linux base image.

        6. Add the following Dockerfile command to the end of the Dockerfile:

            COPY quickstart.sh /

            This adds the quickstart.sh script to the / directory in the image.

        7. Add the following Dockerfile command to the end of the Dockerfile:

            CMD ["/quickstart.sh"]

            This configures the image to execute the /quickstart.sh script when the associated container is created and run.

            The Dockerfile should now look like:

                FROM alpine
                COPY quickstart.sh /
                CMD ["/quickstart.sh"]

        8. Save the file and close nano by pressing the CTRL+X key, then press Y and enter.

        9. In Cloud Shell, run the following command to make the quickstart.sh script executable.

            chmod +x quickstart.sh

        10. In Cloud Shell, run the following command to build the Docker container image in Cloud Build.

            gcloud builds submit --tag gcr.io/${GOOGLE_CLOUD_PROJECT}/quickstart-image .

            **Important**

                Don't miss the dot (".") at the end of the command. The dot specifies that the source code is in the current working directory at build time.

            When the build completes, your Docker image is built and pushed to Container Registry.

        11. In the Cloud Shell, run the following command to list all images.

            gcloud container images list

    Task 3. Building Containers with a build configuration file and Cloud Build

        1. In Cloud Shell enter the following command to clone the repository.

            git clone https://github.com/GoogleCloudPlatformTraining/training-data-analyst

        2. Change to the directory that contains the sample files for this lab.

            cd ~/training-data-analyst/courses/ak8s/02_Cloud_Build/a

            A sample custom cloud build configuration file called cloudbuild.yaml has been provided for you in this directory as well as copies of the Dockerfile and the quickstart.sh script you created in the first task.

        3. In Cloud Shell, execute the following command to view the contents of cloudbuild.yaml.

            cat cloudbuild.yaml

            You will see the following:

                steps:
                - name: 'gcr.io/cloud-builders/docker'
                args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/quickstart-image', '.' ]
                images:
                - 'gcr.io/$PROJECT_ID/quickstart-image'

            This file instructs Cloud Build to use Docker to build an image using the Dockerfile specification in the current local directory, tag it with gcr.io/$PROJECT_ID/quickstart-image ($PROJECT_ID is a substitution variable automatically populated by Cloud Build with the project ID of the associated project) and then push that image to Container Registry.

        4. In Cloud Shell, execute the following command to start a Cloud Build using cloudbuild.yaml as the build configuration file:

            gcloud builds submit --config cloudbuild.yaml .

            The build output to Cloud Shell should be the same as before. When the build completes, a new version of the same image is pushed to Container Registry.

        5. In the Cloud Shell, run the following command to list all images.

            gcloud container images list

            - Then view **quickstart-image**

                gcloud container images list --filter="name:quickstart-image"

                Two versions of quickstart-image are now in the list.

        6. In the Cloud Shell, run the following command to list all the build history.

            gcloud builds list

        7. To view details about a specific build, run the following command:

            gcloud builds describe [BUILD_ID]

            where [BUILD_ID] is the ID of the build for which you want to get details.

            You should see an output similar to the following:

                createTime: '2018-02-22T14:49:54.066666971Z'
                finishTime: '2018-02-22T14:50:05.463758Z'
                id: bcdb9c48-d92c-4489-a3cb-08d0f0795a0b
                images:
                - gcr.io/gcb-docs-project/quickstart-image
                logUrl: https://console.cloud.google.com/gcr/builds/bcdb9c48-d92c-4489-a3cb-08d0f0795a0b?project=gcb-docs-project
                logsBucket: gs://404889597380.cloudbuild-logs.googleusercontent.com
                projectId: gcb-docs-project
                results:
                    buildStepImages:
                    - sha256:a4363bc75a406c4f8c569b12acdd86ebcf18b6004b4f163e8e6293171462a79d
                    images:
                    - digest: sha256:1b2a237e74589167e4a54a8824f0d03d9f66d3c7d9cd172b36daa5ac42e94eb9
                    name: gcr.io/gcb-docs-project/quickstart-image
                    pushTiming:
                        endTime: '2018-02-22T14:50:04.731919081Z'
                        startTime: '2018-02-22T14:50:00.874058710Z'
                    - digest: sha256:1b2a237e74589167e4a54a8824f0d03d9f66d3c7d9cd172b36daa5ac42e94eb9
                        name: gcr.io/gcb-docs-project/quickstart-image:latest
                        pushTiming:
                            endTime: '2018-02-22T14:50:04.731919081Z'
                            startTime: '2018-02-22T14:50:00.874058710Z'
                source:
                    storageSource:
                        bucket: gcb-docs-project_cloudbuild
                        generation: '1519310993665963'
                        object: source/1519310992.2-8465b08c79e14e89bee09adc8203c163.tgz
                sourceProvenance:
                    fileHashes:
                        gs://gcb-docs-project_cloudbuild/source/1519310992.2-8465b08c79e14e89bee09adc8203c163.tgz#1519310993665963:
                        fileHash:
                        - value: -aRYrWp2mtfKhHSyWn6KNQ==
                    resolvedStorageSource:
                        bucket: gcb-docs-project_cloudbuild
                        generation: '1519310993665963'
                        object: source/1519310992.2-8465b08c79e14e89bee09adc8203c163.tgz
                startTime: '2018-02-22T14:49:54.966308841Z'
                status: SUCCESS
                steps:
                - args:
                - build
                - --no-cache
                - -t
                - gcr.io/gcb-docs-project/quickstart-image
                - .
                name: gcr.io/cloud-builders/docker
                status: SUCCESS
                timing:
                    endTime: '2018-02-22T14:50:00.813257422Z'
                    startTime: '2018-02-22T14:50:00.102600442Z'
                timeout: 600s
                timing:
                    BUILD:
                        endTime: '2018-02-22T14:50:00.873604173Z'
                        startTime: '2018-02-22T14:50:00.102589403Z'
                    FETCHSOURCE:
                        endTime: '2018-02-22T14:50:00.087286880Z'
                        startTime: '2018-02-22T14:49:56.962717504Z'
                    PUSH:
                        endTime: '2018-02-22T14:50:04.731958202Z'
                        startTime: '2018-02-22T14:50:00.874057159Z'

    Task 4. Building and Testing Containers with a build configuration file and Cloud Build

        1. In Cloud Shell, change to the directory that contains the sample files for this lab.

            cd ~/training-data-analyst/courses/ak8s/02_Cloud_Build/b

            As before, the quickstart.sh script and the a sample custom cloud build configuration file called cloudbuild.yaml has been provided for you in this directory. These have been slightly modified to demonstrate Cloud Build's ability to test the containers it has build. There is also a Dockerfile present, which is identical to the one used for the previous task.

        2. In Cloud Shell, execute the following command to view the contents of cloudbuild.yaml.

            cat cloudbuild.yaml

            You will see the following:

                steps:
                - name: 'gcr.io/cloud-builders/docker'
                args: [ 'build', '-t', 'gcr.io/$PROJECT_ID/quickstart-image', '.' ]
                - name: 'gcr.io/$PROJECT_ID/quickstart-image'
                args: ['fail']
                images:
                - 'gcr.io/$PROJECT_ID/quickstart-image

            In addition to its previous actions, this build configuration file runs the quickstart-image it has created. In this task, the quickstart.sh script has been modified so that it simulates a test failure when an argument ['fail'] is passed to it.

        3. In Cloud Shell, execute the following command to start a Cloud Build using cloudbuild.yaml as the build configuration file:

            gcloud builds submit --config cloudbuild.yaml .

            You will see output from the command that ends with text like this:

                Finished Step #1
                ERROR
                ERROR: build step 1 "gcr.io/qwiklabs-gcp-00-3c0a50b3c809/quickstart-image" failed: starting step container failed: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"fail\": executable file not found in $PATH": unknown "
                ----------------------------------------------------------------------------------------------------------------------------------------------------------------
                ERROR: (gcloud.builds.submit) build f3e94c28-fba4-4012-a419-48e90fca7491 completed with status "FAILURE"

        4. Confirm that your command shell knows that the build failed:

            echo $?

            The command will reply with a non-zero value. If you had embedded this build in a script, your script would be able to act up on the build's failure.
        