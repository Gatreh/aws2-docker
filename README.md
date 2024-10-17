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
3. Initialize git repo and upload it to github.
4. d
5. 6
6. df
7. f
8. u
```yml
version: 0.2

phases:
  pre_build:
    commands:
      # Fill in ECR information
      - REGISTRY_URI=<uri>
      - IMAGE_NAME=<image-name>
      - REGION=eu-west-1
      # Fill in ECS information
      - CONTAINER_NAME=<container-name> # Not same as Image name, gets confusing.
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
- AWS + AWS CLI
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