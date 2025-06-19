Azure Pipeline


1. trigger
Specifies which branches will automatically start the pipeline.



		trigger:
                  - main		
  
2. pr
Defines branches that trigger the pipeline on pull requests.

		pr:
		  - develop

3. pool
Specifies the agent or VM image to run the pipeline.

		pool:
		  vmImage: 'ubuntu-latest'

4. variables
Defines variables to use throughout the pipeline.

		variables:
		  buildConfiguration: 'Release'
  

5. stages
Divides the pipeline into major phases (e.g., Build, Test, Deploy).

		stages:
		  - stage: Build
		    jobs:
		      ### jobs go here
	  

6. jobs
A collection of steps that run together on the same agent.

		jobs:
		  - job: BuildJob
		    steps:
		      ### steps go here
	  

7. steps
A list of tasks or scripts to execute in a job.

		steps:
		  - script: echo Hello
		  - task: PublishBuildArtifacts@1
  

8. task
A predefined action, such as building code or publishing artifacts.

		- task: PublishBuildArtifacts@1
		  inputs:
		    pathToPublish: 'dist'
		    artifactName: 'drop'
	
9. script
Runs a shell or batch script.

		- script: npm install
		  displayName: 'Install dependencies'
  
10. dependsOn
Specifies dependencies between stages or jobs.

		- stage: Test
		  dependsOn: Build
  
11. condition
Controls whether a step, job, or stage runs.

		- script: echo "Only run if previous step succeeded"
		  condition: succeeded()
  

Summary Table:

# Element	Purpose
- trigger	Branches that start the pipeline
- pr	Branches that trigger on pull requests
- pool	Agent/VM image to run the pipeline
- variables	Define reusable values
- stages	Major pipeline phases
- jobs	Group of steps on the same agent
- steps	Individual actions (script/task)
- task	Predefined action
- script	Custom shell/batch command
- dependsOn	Set dependencies between jobs/stages
- condition	Control execution based on conditions
