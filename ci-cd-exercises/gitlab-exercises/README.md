# devops-exercises

## GitLab Ejercicios

## 1. CI/CD de una aplicación spring

En resumen, la pipeline de CI/CD, debe hacer la build de la aplicación generando los ficheros jar, hacer los tests de maven y finalmente dockerizar la app (el dockerfile ya se proporciona en el repo) y hacer un deploy en local.

Creamos un fichero [.gitlab-ci.yml](https://github.com/igoralvarez/devops-exercises/blob/main/ci-cd-exercises/gitlab-exercises/exercise-1/.gitlab-ci.yml) con el siguiente contenido:

```bash
variables:
  IMAGE_NAME: "maven:3.6.3-jdk-8-slim"
  REGISTRY_PATH: "$CI_REGISTRY/$CI_PROJECT_PATH"

stages:
  - maven:build
  - maven:test
  - docker:build
  - deploy

maven:build:
  image: $IMAGE_NAME
  stage: maven:build
  script:
    - mvn clean package
  artifacts:
    when: on_success
    paths:
      - "target/*.jar"

maven:ut:
  image: $IMAGE_NAME
  stage: maven:test
  dependencies:
    - maven:build
  script:
    - mvn verify
  artifacts:
    when: on_success
    paths:
      - "target/"

build:
  stage: docker:build
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_JOB_TOKEN $REGISTRY_PATH
  script:
    - echo $CI_REGISTRY $CI_REGISTRY_USER $CI_REGISTRY_PASSWORD
    - docker build -t $REGISTRY_PATH:$CI_COMMIT_SHA .
    - docker push $REGISTRY_PATH:$CI_COMMIT_SHA

deploy:
   stage: deploy
   before_script:
     - docker login -u $CI_REGISTRY_USER -p $CI_JOB_TOKEN $REGISTRY_PATH
   script:
     - if [[ $(docker ps -a --filter "name=springapp" --format '{{.Names}}') == "springapp" ]]; then  docker container rm -f springapp; else echo "No existe";  fi
     - docker run --name "springapp" -d -p 8080:8080 $REGISTRY_PATH:$CI_COMMIT_SHA

```

Podemos comprobar que los 4 jobs se han ejecutado correctamente:

![ejecución pipeline correcta](./images/image1.png)

Si accedemos a http://localhost:8080/ podemos comprobar que está en ejecución:

![en ejecución](./images/image2.png)

## 2. Crear un usuario nuevo y probar que no puede acceder al proyecto anteriormente creado

Guest:

- Commit: No.
- Ejecutar pipeline manualmente: No. Aunque se muestran las ya ejecutadas.
- Push and pull del repo: Pull si el proyecto es público.
- Merge request: No.
- Acceder a la administración: No.

Reporter:

- Commit: No.
- Ejecutar pipeline manualmente: No.
- Push and pull del repo: Solo pull.
- Merge request: Si.
- Acceder a la administración: No.

developer:

- Commit: Si.
- Ejecutar pipeline manualmente: Si.
- Push and pull del repo: Si.
- Merge request: Si.
- Acceder a la administración: No.

maintainer:

- Commit: Si.
- Ejecutar pipeline manualmente: Si.
- Push and pull del repo: Si.
- Merge request: Si.
- Acceder a la administración: Si.

## 3. Crear un nuevo repositorio, que contenga una pipeline, que clone otro proyecto, springapp anteriormente creado. Realizarlo de las siguientes maneras:

Con el método de CI job permissions model
Creamos un fichero [.gitlab-ci.yml](https://github.com/igoralvarez/devops-exercises/blob/main/ci-cd-exercises/gitlab-exercises/exercise-3-1/.gitlab-ci.yml). con el siguiente contenido:

```bash

stages:
    - cloneProject

cloneProject:springapp:
    image:
        name: alpine/git:latest
        entrypoint: [""]
    stage: cloneProject
    script:
        - git clone http://gitlab-ci-token:${CI_JOB_TOKEN}@gitlab.local:8888/bootcamp/springapp.git

```

Al ejecutarse:

![ejecución correcta](./images/image3.png)



Con el método deploy keys

Generamos las claves.

En User Settings -> SSH Keys añadimos la clave pública.

Creamos un fichero [.gitlab-ci.yml](https://github.com/igoralvarez/devops-exercises/blob/main/ci-cd-exercises/gitlab-exercises/exercise-3-2/.gitlab-ci.yml) con el siguiente contenido:

```bash
stages:
    - cloneProject

cloneProject:springapp:
    image:
        name: alpine/git:latest
        entrypoint: [""]
    stage: cloneProject
    before_script:
        - 'command -v ssh-agent >/dev/null || ( apt-get update -y && apt-get install openssh-client -y )'
        - eval $(ssh-agent -s)
        - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
        - mkdir -p ~/.ssh
        - chmod 700 ~/.ssh
        - ssh-keyscan gitlab.local >> ~/.ssh/known_hosts
        - chmod 644 ~/.ssh/known_hosts
    script:
         - ssh git@gitlab.local
         - git clone git@gitlab.local:bootcamp/springapp.git
```

Creamos una variable de entorno con la clave privada:

![añadir varable entorno](./images/image4.png)

Al ejecutarse:

![ejecución correcta](./images/image5.png)