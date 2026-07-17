# AWS-CI-CD-CodePipeline
A stage by stage demo on creating a full code pipeline CI/CD project from start to finish, including the commit, build and deploy stage.

I will also go through the issues I faced in the build and how I worked through them

Disclamer: This project was taken from a Adrian Cantrill demo, you canb find his youtube tutorial of this project [here](https://www.youtube.com/watch?v=MDMH_XXDbrI)


# This project includes 5 steps:

STAGE 1 : Configure Security & Create a CodeCommit Repo

STAGE 2 : Configure CodeBuild to clone the repo, create a container image and store on ECR

STAGE 3 : Configure a CodePipeline with commit and build steps to automate build on commit.

STAGE 4 : Create an ECS Cluster, TG's , ALB and configure the code pipeline for deployment to ECS Fargate

STAGE 5 : CLEANUP



# **Instructions**

[Stage1](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Instructions/Stage%201.md)

[Stage2](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Instructions/Stage%202.md)

[Stage3](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Instructions/Stage%203.md)

[Stage4](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Instructions/Stage%204.md)

[Stage5](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/main/Instructions/Stage%205.md)



**Architecture Diagrams**

[Stage1](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Diagrams/Stage%201%20-%20CICD.png) - PNG

[Stage2](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Diagrams/Stage%202%20-%20CICD.png) - PNG

[Stage3](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Diagrams/Stage%203%20-%20CICD.png) - PNG

[Stage4](https://github.com/Branyb/AWS-CI-CD-CodePipeline/blob/5567aa1bd3f8e916517036b8e24cfa4da3fe38dc/Diagrams/Stage%204%20-%20CICD.png) - PNG
