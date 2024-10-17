[comment]:<1. Sätt upp en skalbar värdmiljö för en containerbaserad webbapplikation och beskriv steg för steg hur du går tillväga>
[//]: <Använd AWS ECS när du implementerar lösningen.>
[//]: <Beskriv vad de olika komponenterna i din lösning har för uppgift och syfte>
[//]: <Redogör för vilka molntjänster du utnyttjat.>
[//]: <Beskriv hur du använder IaC och automation - om du gör det.>
[//]: <2. Sätt upp en CI/CD Pipeline för att driftsätta din applikation.>
[//]: <Använd AWS när du implementerar lösningen.>
[//]: <Beskriv vad de olika komponenterna i din pipeline har för uppgift och syfte>
[//]: <Redogör för vilka molntjänster du utnyttjat.>
[//]: <Gör en uppdatering av din applikation och verifiera att ändringen propagerar genom pipelinen och driftssätts automatiskt i värdmiljön.>
Steg 
1. Create a basic website file to run in the docker image.
2. Create a docker image using Amazon Linux 2023, install NGINX on it and expose port 80.
```docker
FROM amazonlinux:2023

RUN dnf update -y
RUN dnf install -y nginx
COPY ./html /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```
3. Create github repo on githubs website and initialize git repo and upload it to github.
```bash
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:<git user>/<repo name>.git
git push origin main
```
4. Create an ECR using this command and take note of the underlined information:
```bash
aws ecr create-repository --repository-name aws2-docker
```
![ECR Creation Data](./img/amazon-ecr-repository)

5. It's time to use the information you have noted down before into this buildspec file for our code pipeline
```yml
version: 0.2

phases:
  pre_build:
    commands:
      # Fill in ECR information
      - REGISTRY_URI=767397794379.dkr.ecr.eu-west-1.amazonaws.com # Registry URI
      - IMAGE_NAME=aws2-docker # Repository Name
      - REGION=eu-west-1 # Region taken from either Repository Arn or Repository Uri.
      # Fill in ECS information
      - CONTAINER_NAME=AWS2_DockerContainer # This will be the name you should use for your task definition container name.
      # -----------------------
      - IMAGE=$REGISTRY_URI/$IMAGE_NAME
      - COMMIT=$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | cut -c 1-8)
      # Log in to docker
      - echo $DOCKERHUB_PASSWORD | docker login -u $DOCKERHUB_USERNAME --password-stdin
      - aws ecr get-login-password --region $REGION | docker login --username AWS --password-stdin $REGISTRY_URI
  build:
    commands:
      - docker build --tag $IMAGE .
      - docker tag $IMAGE $IMAGE:$COMMIT
  post_build:
    commands:
      - docker push $IMAGE
      - docker push $IMAGE:$COMMIT
      # Create imagedefinitions.json. This is used by ECS to know which docker image to use.
      - printf '[{"name":"%s","imageUri":"%s"}]' $CONTAINER_NAME $IMAGE:$COMMIT > imagedefinitions.json
artifacts:
  files:
    # Put imagedefinitions.json in the artifact zip file
    - imagedefinitions.json
```
5. Create a Build Project
    - Click on create project.
    - Source
        - Source should be Github
        - Connect github by clicking Manage default source credential, it will open a new Tab. 
        - Use the Github App and click the linked text saying "create a new GitHub connection".
        - A new window will pop up where you log into github, when it is created you press select it in the dropdown and press save.
        - Clck the Repository and it should pop up with a list of repositories on your account.
        - Select the repo for this project. Under Source version make sure to select or type the branch you'll be using.
    - Use a Webhook
    - Environment 
        - Operating system to Ubuntu
        - Runtime to Standard
        - Image to aws/codebuild/standard:7.0
        - Under Additional configuration you need to check the box under Privileged and
        - Add two environment variables, Your docker username and your docker password as DOCKERHUB_USERNAME and DOCKERHUB_PASSWORD respectively. The username is case sensitive and **HAS** to be lowercase.
    - Create a new service role and note the name.
    - Under the Buildspec section you need to change to using a buildspec file.
    - Create project.

    - To finish off this you need to go to IAM, Roles, Click the newly made service role and add the managed policy AmazonEC2ContainerRegistryPowerUser to it.
    - Start build
- Verify that the image shows up under ECR's 
6. Create Elastic container service with a task definition, cluster and service.
    1. ECS Task
        - Use AWS fargate for the task definition
        - Use the same Container definition name from the buildspec.
        - When adding the container definition name you will need the <Repository URI>/<Image name> from the previous image.
        - Press Create
    2. ECS Cluster
        - Cluster name and create it.
    3. ECS Service
        - Create a service in your cluster.
        - Family name has to be entered but ultimately doesn't matter.
        - Service name
        - Set Desired Tasks to more than 1.
        - Expand networking and use or create a new security group that's open to HTTP.
        - Open up the section for Load Balancing, Select an Application Load Balancer and name it.
        - Create the service.
7. After a few minutes go into your ECS cluster, Click the service and check the tasks tab, in here you click the task name and you'll be able to find the IP for your docker container. Verify it is up and working by clicking the IP. Then go to EC2 -> Load Balancer -> Copy the DNS name of your load balancer and open a new tab to verify the load balancer works.
8. Create a CodePipeline
    - Choose a Build a custom pipeline then next.
    - Name it as usual and click next.
    - Source
        - Github Version 1
        - Connect to github, should be 2 clicks.
        - Choose the repository for your project and the branch then click next.
    - Build
        - Choose Other Build providers and Select AWS Codebuild fromthe dropdown.
        - Choose the Codebuild project we made earlier.
        - Click next.
    - Deploy Provider should be Amazon ECS.
    - Cluster and Service name should be the projects names.
9. f
10. u

Try to keep to a naming scheme like <project-name><service-type> and descriptive.
# AWS 2 - Skalbar Dockermiljö
### Index
1. [Inledning](#inledning)
2. [Krav](#krav)
3. [Systemarkitektur - Översikt](#systemarkitektur---översikt)
    - [Komponenter](#komponenter)
    - [Utnyttjade molntjänster](#utnyttjade-molntjänster)
1. [Provisionering av driftmiljö och driftsättning av applikation](#provisionering-av-driftmiljö-och-driftsättning-av-applikation)
1. [Slutsats](#slutsats)
2. [Appendix](#appendix)
3. [Referenser](#referenser)
### Verktyg och tjänster
- Obsidian
- VSCode
- GitHub
- AWS + AWS CLI
- Docker
## Inledning
Målet med denna uppgiften 
## Krav
## Systemarkitektur - Översikt
### Komponenter
### Utnyttjade molntjänster
## Provisionering av driftmiljö och driftsättning av applikation
## Slutsats
## Appendix
## Referenser