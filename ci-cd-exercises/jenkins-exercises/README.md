
## 1. CI/CD de un Java + Gradle
- Creamos el siguiente [Jenkinsfile](https://github.com/igoralvarez/devops-exercises/blob/main/ci-cd-exercises/jenkins-exercises/exercise-1/calculator/Jenkinsfile):

```bash
pipeline {
  agent any
  stages {
    stage('Compile') {
      steps {
        dir('./ci-cd-exercises/jenkins-exercices/exercise-1/calculator') {
          sh '''
            chmod +x gradlew
            ./gradlew compileJava
          '''
        }
      }
    }
    stage('Unit Test') {
      steps {
        dir('./ci-cd-exercises/jenkins-exercices/exercise-1/calculator') {
          sh '''
            ./gradlew test
          '''
        }
      }
    }
  }
}
```


## 2. Modificar la pipeline para que utilice la imagen Docker de Gradle como build runners

- Creamos el siguiente [Jenkinsfile](https://github.com/igoralvarez/devops-exercises/blob/main/ci-cd-exercises/jenkins-exercises/exercise-2/calculator/Jenkinsfile):

```bash
pipeline {
  agent {
    docker {
      image 'gradle'
    }
  }
  environment {
    EXERCISE='Exercise 2'
    MYPATH='./ci-cd-exercises/jenkins-exercises/exercise-2/calculator'
  }
  stages {
    stage('Compile') {
      steps {
        dir("$MYPATH") {
          echo "This is the build number $BUILD_NUMBER of $EXERCISE"
          sh '''
            chmod +x gradlew
            ./gradlew compileJava
          '''
        }
      }
    }
    stage('Unit Test') {
      steps {
        dir("$MYPATH") {
          sh './gradlew test'
        }
      }
    }
  }
}
```
