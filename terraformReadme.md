# What is Terraform?

HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.

# Terraform Cloud

Terraform Cloud is the managed solution offered by Hashicorp to run Terraform configurations 💡. It is in some manner a “CI/CD tool” to manage Infrastructure as Code (IaC). It eliminates the need for you to configure a dedicated task in your CI/CD (Jenkins, Bamboo, Circle CI, etc.) and also provided a tailor-made UI, remote state management, and private module registry.

# Terraform and GitHub Actions


https://learn.hashicorp.com/tutorials/terraform/github-actions

.github/workflows/terraform.yml

    on:
      push:
        branches:
          - main
      pull_request:

      jobs:
      terraform:
        name: 'Terraform'
        runs-on: ubuntu-latest
        steps:
        - name: Checkout
            uses: actions/checkout@v3

    #Setup Terraform retrieves the Terraform CLI used in the GitHub action workflow.In addition, this step loads the TF_API_TOKEN secret as an environment variable, enabling the Terraform CLI to authenticate to Terraform Cloud.
      - name: Setup Terraform
          uses: hashicorp/setup-terraform@v1
          with:
          # terraform_version: 0.13.0
          cli_config_credentials_token: ${{ secrets.TF_API_TOKEN }}

      - name: Terraform Format   # Terraform Format checks whether the configuration has been properly formatted. If the configuration isn't properly formatted this step will produce an error.
          id: fmt
          run: terraform fmt -check
        - name: Terraform Init    #Terraform Init initializes the configuration used in the GitHub action workflow.
          id: init
          run: terraform init
      - name: Terraform Validate		#Terraform Validate validates the configuration used in the GitHub action workflow.
        id: validate
        run: terraform validate -no-color	  
      - name: Terraform Plan
        id: plan
        if: github.event_name == 'pull_request'
        run: terraform plan -no-color -input=false
        continue-on-error: true	  
      - name: Update Pull Request
        uses: actions/github-script@6
        if: github.event_name == 'pull_request'
        env:
        PLAN: "terraform\n${{ steps.plan.outputs.stdout }}"
        with:
        github-token: ${{ secrets.GITHUB_TOKEN }}
        script: |
          const output = `#### Terraform Format and Style 🖌\`${{ steps.fmt.outcome }}\`
          #### Terraform Initialization ⚙️\`${{ steps.init.outcome }}\`
          #### Terraform Plan 📖\`${{ steps.plan.outcome }}\`
          #### Terraform Validation 🤖\`${{ steps.validate.outcome }}\`

          <details><summary>Show Plan</summary>

          \`\`\`\n
          ${process.env.PLAN}
          \`\`\`

          </details>

          *Pushed by: @${{ github.actor }}, Action: \`${{ github.event_name }}\`*`;

          github.rest.issues.createComment({
          issue_number: context.issue.number,
          owner: context.repo.owner,
          repo: context.repo.repo,
          body: output
          })	  

      - name: Terraform Plan Status
        if: steps.plan.outcome == 'failure'
        run: exit 1
      - name: Terraform Apply
        if: github.ref == 'refs/heads/main' && github.event_name == 'push'
        run: terraform apply -auto-approve -input=false  


# Why Pipeline

Now you might be asking, “Why should I add our Terraform configuration to GitHub Actions or other CI/CD Pipelines?” Here are some reasons:

Pipelines create more visibility. When working on managing infrastructure with Terraform on a team, you can easily see what is running and when it ran. When running locally, only you have that visibility.

Pipelines create traceability. When running Terraform in a pipeline, they usually store logs. This allows you to review old builds and their outputs at your convenience. 

Pipelines create repeatability. When you configure a pipeline, it should do the same action every time. This will make debugging and troubleshooting much easier.

And finally, they create simplicity. A pipeline can essentially take the place of your local instance. This way you don’t have to set up local dependencies and what not.

# Pipeline

The first thing you’ll need to do before your GitHub Actions can run is to add your AWS credentials to the repository. To do this you will need to follow these steps:

Navigate to your repository and select the Settings tab.
Once there you should see on the left a Secrets section third from the bottom of the list, click on that.
Click on the New repository secret button.
Add your AWS_SECRET_ACCESS_KEY and click the Add secret button.
Repeat step 3 and add your AWS_ACCESS_KEY_ID and click the Add secret button.

      name: Deploy Infrastructure

      on:
        push:
        branches:
          - master

      jobs:
        tf_fmt:
        name: Deploy Site
        runs-on: ubuntu-latest
        steps:

        - name: Checkout Repo
          uses: actions/checkout@v1

        - name: Terraform Init
          uses: hashicorp/terraform-github-actions/init@v0.4.0
          env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          TF_ACTION_WORKING_DIR: 'terraform'
          AWS_ACCESS_KEY_ID:  ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY:  ${{ secrets.AWS_SECRET_ACCESS_KEY }}

        - name: Terraform Validate
          uses: hashicorp/terraform-github-actions/validate@v0.3.7

        - name: Terraform Apply
          uses: hashicorp/terraform-github-actions/apply@v0.4.0
          env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          TF_ACTION_WORKING_DIR: 'terraform'
          AWS_ACCESS_KEY_ID:  ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY:  ${{ secrets.AWS_SECRET_ACCESS_KEY }}

        - name: Sync S3
          uses: jakejarvis/s3-sync-action@master
          env:
          SOURCE_DIR: './src'
          AWS_REGION: 'us-east-1'
          AWS_S3_BUCKET: '[BUCKET_NAME_HERE]'
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
				
				
https://aninditabasak.medium.com/managing-ci-cd-on-terraform-with-github-actions-workflows-abf0bdeb4877https://aninditabasak.medium.com/managing-ci-cd-on-terraform-with-github-actions-workflows-abf0bdeb4877

https://faun.pub/what-is-terraform-cloud-and-why-you-might-need-it-c9847fb8f6e6
