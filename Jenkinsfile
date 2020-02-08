pipeline {
  agent any
  stages {
      stage('Lint HTML') {
        steps {
          sh 'pwd && ls && tidy -q -e *.html'
        }
      }
      stage('Build') {
        steps {
          sh 'echo "Hello World"'
          sh '''
                    echo "Multiline shell steps works too"
                    ls -lah
                 '''
        }
      }
      stage('Upload to AWS') {
          steps {
            withAWS(region:'us-east-1',credentials:'aws-static') {
              s3Upload(pathStyleAccessEnabled:true, payloadSigningEnabled: true, file:'index.html', bucket:'jenkins-html-publisher-bucket', path:'index.html')
            }
          }
       }
      stage('Check HTML returns 200') {
          steps {
            sh '''
                URL=http://jenkins-html-publisher-bucket.s3-website-us-east-1.amazonaws.com/
                status_code=$(curl --write-out %{http_code} --silent --output /dev/null &URL)
                if [[ "$status_code" == "200" ]] ; then
                  echo "Site is up and running"
                else
                  echo "Something is wrong with index.html after deployment ${status_code}"
                  exit 0
                fi
                   '''
          }
        }
  }
}
