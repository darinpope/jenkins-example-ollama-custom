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
          ollama rm the-butler:latest || true
          ollama rm the-butler:v0.2 || true
          ollama rm darinpope/the-butler:latest || true
          ollama rm darinpope/the-butler:v0.2 || true
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
        sh '''
          ollama run mistral:v0.2 "hi" --verbose > mistral-hi.txt
          cat mistral-hi.txt
        '''
      }
    }
    stage('build the-butler image') {
      steps {
        sh '''
          ollama create the-butler:latest -f ./Modelfile
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
        sh '''
          ollama run the-butler:latest "hi" --verbose > the-butler-hi.txt
          cat the-butler-hi.txt
        '''
      }
    }
    stage('put the models into the correct namespace') {
      steps {
        sh '''
          ollama cp the-butler:latest darinpope/the-butler:latest
          ollama cp the-butler:latest darinpope/the-butler:v0.2
        '''
      }
    }
    stage('list the models') {
      steps {
        sh 'ollama list'
      }
    }
    stage('push the model to Ollama') {
      steps {
        sh '''
          ollama push darinpope/the-butler:latest
          ollama push darinpope/the-butler:v0.2
        '''
      }
    }
  }
  post {
    always {
      sh '''
        ollama rm the-butler:latest || true
        ollama rm the-butler:v0.2 || true
        ollama rm darinpope/the-butler:latest || true
        ollama rm darinpope/the-butler:v0.2 || true
        rm -f the-butler-hi.txt
        rm -f mistral-hi.txt
      '''
    }
  }
}