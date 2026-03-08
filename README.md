[README.md](https://github.com/user-attachments/files/25824282/README.md)
<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Continuous Integration with CodeBuild

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-devops-codebuild-updated)

**Author:** Saqib Hossain  
**Email:** saqibh49@gmail.com

---

![Image](http://learn.nextwork.org/grateful_white_lucky_bat/uploads/aws-devops-codebuild-updated_35588a47)

---

## Introducing Today's Project!

In this project, I will demonstrate how to set up AWS CodeBuild, connect it to my GitHub repo, and define a build process using a buildspec.yml file. I'm doing this project to learn how to automate the build and test stages of a CI/CD pipeline instead of compiling and testing everything manually.

### Key tools and concepts

Services I used were EC2, CodeBuild, CodeArtifact, S3, IAM, CloudWatch, GitHub, and CodeConnections. Key concepts I learnt include continuous integration, buildspec.yml files, build phases, connecting AWS to GitHub securely, storing build artifacts in S3, troubleshooting permission errors with IAM roles, and how CodeBuild automates the build process so you don't have to compile manually.

### Project reflection

This project took me approximately 2 hours. The most challenging part was troubleshooting the back-to-back build failures — first the missing buildspec file, then the permissions error with CodeArtifact. It was most rewarding to finally see that artifact sitting in my S3 bucket after the successful build, knowing the whole pipeline from GitHub to CodeBuild to S3 was working end to end.

This project is part four of a series of DevOps projects where I'm building a CI/CD pipeline! I'll be working on the next project soon, where I'll continue adding more stages to the pipeline and bringing it even closer to a fully automated workflow.

---

## Setting up a CodeBuild Project

CodeBuild is a continuous integration service, which means it automatically builds and tests your code every time changes are pushed. Engineering teams use it because it takes the manual work out of compiling and testing — instead of doing it yourself every time, CodeBuild handles it automatically so you can focus on writing code.

My CodeBuild project's source configuration means where CodeBuild pulls the code from when it starts a build. I selected GitHub as my source provider and connected it to my web app's repository, so every time a build runs, CodeBuild grabs the latest code straight from my repo.

![Image](http://learn.nextwork.org/grateful_white_lucky_bat/uploads/aws-devops-codebuild-updated_fewgrhte)

---

## Connecting CodeBuild with GitHub

There are multiple credential types for GitHub, like personal access tokens, OAuth, and GitHub App. I used GitHub App because it's the most secure option — it gives AWS fine-grained permissions to access only the repos I choose, rather than giving broad access to my entire GitHub account.

The service that helped connect my AWS account with GitHub is AWS CodeConnections. It sets up a secure link between the two so services like CodeBuild can access my GitHub repos without me having to manage credentials manually every time a build runs.

![Image](http://learn.nextwork.org/grateful_white_lucky_bat/uploads/aws-devops-codebuild-updated_a7c98e2d)

---

## CodeBuild Configurations

### Environment

My CodeBuild project's Environment configuration means the setup of the machine that will actually run my builds. It includes settings like the provisioning model (on-demand or reserved), the operating system, compute type, and the container image — basically defining what kind of virtual build server CodeBuild spins up every time it runs.

### Artifacts

Build artifacts are the output files generated after CodeBuild finishes building my code — like compiled files, packaged apps, or deployment-ready files. They're important because they're what actually gets deployed to production. My build process will create a WAR file, which is a packaged version of my Java web app. To store them, I created an S3 bucket that CodeBuild will automatically upload the artifacts to after each build.

### Packaging

When setting up CodeBuild, I also chose to package artifacts in a zip file because it bundles everything into one clean file, making it easier to download, transfer, and deploy instead of dealing with a bunch of loose files.

### Monitoring

For monitoring, I enabled CloudWatch Logs, which is an AWS service that captures and stores log data from my builds. This way, if something goes wrong during a build, I can check the logs to see exactly what happened and where it failed.

---

## buildspec.yml

My first build failed because CodeBuild didn't have any instructions telling it how to build my project — it pulled the code from GitHub but had no idea what to do with it. A buildspec.yml file is needed because it's basically the recipe that tells CodeBuild the exact commands to run during each phase of the build, like installing dependencies, compiling the code, and packaging the output.

The first two phases in my buildspec.yml file are install and pre_build. Install tells CodeBuild to use Amazon Corretto 8 as the Java runtime, and pre_build runs setup commands like retrieving a fresh CodeArtifact authorization token so Maven can pull dependencies during the build.

The third phase in my buildspec.yml file is the build phase, which is where the actual compiling happens — it runs mvn compile using my settings.xml to build my Java web app with dependencies from CodeArtifact.

The fourth phase in my buildspec.yml file is post_build, which runs after compiling is done. It packages my app into a WAR file using mvn package, and the artifacts section tells CodeBuild to save that WAR file so it can be uploaded to S3.

![Image](http://learn.nextwork.org/grateful_white_lucky_bat/uploads/aws-devops-codebuild-updated_35588a47)

---

## Success!

My second build also failed, but with a different error that said CodeBuild couldn't retrieve the authorization token from CodeArtifact because it didn't have the right permissions. To fix this, I attached my CodeArtifact IAM policy to CodeBuild's service role so it's authorized to access the repository and pull dependencies during the build.

To resolve the second error, I attached my CodeArtifact IAM policy to CodeBuild's service role, giving it permission to retrieve tokens and read packages from my repository. When I built my project again, I saw the build succeed — all phases completed without errors, and my WAR file was packaged and uploaded to S3 as expected.

To verify the build, I checked my S3 bucket and found the build artifact (nextwork-devops-cicd-artifact) sitting right there. Seeing the artifact tells me that the entire build pipeline worked — CodeBuild pulled my code from GitHub, compiled it, packaged it into a WAR file, and uploaded it to S3 successfully.

![Image](http://learn.nextwork.org/grateful_white_lucky_bat/uploads/aws-devops-codebuild-updated_d9cc6191)

---

## Automating Testing

---

---
