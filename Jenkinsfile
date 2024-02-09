pipeline {
  agent any
  stages {
    stage('Verify tooling') {
      steps {
        sh '''
          ollama --version
        '''
      }
    }
    stage('cleanup old images') {
      steps {
        sh '''
          ollama rm the-butler:latest the-butler:v0.2
        '''
      }
    }
    stage('pull the most recent mistral v0.2') {
      steps {
        sh 'ollama pull mistral:v0.2'
      }
    }
    stage('verify the image is available') {
      steps {
        sh 'ollama list'
      }
    }
    stage('check that the model is responding') {
      steps {
        sh 'ollama run mistral:v0.2 "hi" --verbose'
      }
    }
    stage('build the-butler image') {
      steps {
        sh '''
          ollama create the-butler:latest -f ./Modelfile
          ollama cp the-butler:latest the-butler:v0.2
        '''
      }
    }
    stage('double-check the models exist') {
      steps {
        sh 'ollama list'
      }
    }
    stage('check that the-butler is responding') {
      steps {
        sh 'ollama run the-butler:v0.2 "hi" --verbose'
      }
    }
    stage('push the model to Ollama') {
      steps {
        sh '''
          ollama push the-butler:latest
          ollama push the-butler:v0.2
        '''
      }
    }
  }
}