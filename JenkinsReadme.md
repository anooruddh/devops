### Jfrog Pipeline

** Artifactory Plugins **

 rtBuildAppend: Build append
 
 rtBuildInfo: Create build info
 
 rtBuildTrigger: Trigger Artifactory build
 
 rtCollectIssues: Collect issues
 
 rtDeleteProps: Delete properties
 
 rtDockerPush: run Artifactory docker push
 
 rtDownload: Download artifacts
 
       pipeline {
        agent any

        stages {
            stage ('Clone') {
                steps {
                    git branch: 'master', url: 'https://github.com/jfrog/project-examples.git'
                }
            }

            stage ('Upload file') {
                steps {
                    rtUpload (
                        // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
                        serverId: SERVER_ID,
                        spec: """{
                                "files": [
                                        {
                                            "pattern": "jenkins-examples/pipeline-examples/resources/ArtifactoryPipeline.zip",
                                            "target": "libs-snapshot-local"
                                        }
                                    ]
                                }"""
                    )
                }
            }

            stage ('Publish build info') {
                steps {
                    rtPublishBuildInfo (
                        serverId: SERVER_ID
                    )
                }
            }

            stage ('Set output resources') {
                steps {
                    // 'jfPipelines' step will be skipped if the build is not triggered by JFrog Pipelines.
                    jfPipelines(
                        /**
                        * Sets the output resources to send to JFrog Pipelines.
                        * 'pipelinesBuildInfo' is the build-info resource defined in JFrog Pipelines.
                        */
                        outputResources: """[
                            {
                                "name": "pipelinesBuildInfo",
                                "content": {
                                    "buildName": "${env.JOB_NAME}",
                                    "buildNumber": "${env.BUILD_NUMBER}"
                                }
                            }
                        ]"""
                    )
                }
            }
        }
    }
    
### Jenkins Pipeline Concepts

** Term	Description

Pipeline	The pipeline is a set of instructions given in the form of code for continuous delivery and consists of instructions needed for the entire build process. With pipeline, you can build, test, and deliver the application.

Node	The machine on which Jenkins runs is called a node. A node block is mainly used in scripted pipeline syntax.

Stage	A stage block contains a series of steps in a pipeline. That is, the build, test, and deploy processes all come together in a stage. Generally, a stage block is used to visualize the Jenkins pipeline process.

Step	A step is nothing but a single task that executes a specific process at a defined time. A pipeline involves a series of steps.    

### A Build Lifecycle is Made Up of Phases
Each of these build lifecycles is defined by a different list of build phases, wherein a build phase represents a stage in the lifecycle.

For example, the default lifecycle comprises of the following phases (for a complete list of the lifecycle phases, refer to the Lifecycle Reference):

       validate - validate the project is correct and all necessary information is available
       compile - compile the source code of the project
       test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
       package - take the compiled code and package it in its distributable format, such as a JAR.
       verify - run any checks on results of integration tests to ensure quality criteria are met
       install - install the package into the local repository, for use as a dependency in other projects locally
       deploy - done in the build environment, copies the final package to the remote repository for sharing with other developers and projects.

For a basic Java project, we just need the Simplest POM, that defines a groupId, artifactId, and version, the three required coordinates for every project

## Maven: How do I rename the war file for the project?
use the finalName tag to define/change the name of the war file in the web module that produces the war. See the below example.

       <build>
         <finalName>MyWebApp</finalName>
       </build>
-----------------------------------------------------------------------------------------------------------------------------------------------------------

