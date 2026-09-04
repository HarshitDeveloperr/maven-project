pipeline
{

agent {
  label 'server1'
}

parameters {
    choice choices: ['dev', 'prod'], name: 'select_environment'
}

environment{
    NAME = "piyush"
}
tools {
  maven 'mymaven'
}

stages{

    stage('build')
    {
        steps {
            script{
                def file = load "script.groovy"
                file.hello()
            }
            bat 'mvn clean package -DskipTests=true'
           
        }

        

    }

    stage('test')
    { 
        parallel {
            stage('testA')
            {
                agent { label 'server1' }
                steps{
                    echo " This is test A"
                    bat "mvn test"
                }
                
            }
            stage('testB')
            {
                agent { label 'server1' }
                steps{
                echo "this is test B"
                bat "mvn test"
                }
            }
        }
        post {
        success {
             dir("webapp/target/")
            {
            stash name: "maven-build", includes: "*.war"
                 }
                 }
            }

    }

    stage('deploy_dev')
    {
        when { expression {params.select_environment == 'dev'}
        beforeAgent true}
        agent { label 'Built-In Node' }
        steps
        {
            dir('C:\\deploy\\dev')
            {
                unstash "maven-build"
            }
            bat """
           
            jar -xvf webapp.war
            """
        }
    }

    stage('deploy_prod')
    {
      when { expression {params.select_environment == 'prod'}
        beforeAgent true}
        agent { label 'server1' }
        steps
        {
             timeout(time:5, unit:'DAYS'){
                input message: 'Deployment approved?'
             }
           dir('C:\\deploy\\prod')
            {
                unstash "maven-build"
            }
            bat """
            
            jar -xvf webapp.war
            """
        }  
    }

   

    
}

}
