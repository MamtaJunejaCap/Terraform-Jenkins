pipeline {

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
    } 
    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    }

   agent  any
    tools {
  terraform 'terraform'
    }
    stages {
        stage('checkout') {
            steps {
                 script{
                        dir("terraform")
                        {
                            git "https://github.com/MamtaJunejaCap/Terraform-Jenkins.git"
                        }
                    }
                }
            }
        stage ("terraform init") {
            steps {
                bat ('terraform init') 
            }
        }

        stage('Plan') {
             steps {                
                bat ('terraform plan -out tfplan')
                bat ('terraform show -no-color tfplan > tfplan.txt')
            }
        }
        stage('Approval') {
           when {
               not {
                   equals expected: true, actual: params.autoApprove
               }
           }

           steps {
               script {
                    def plan = readFile 'terraform/tfplan.txt'
                    input message: "Do you want to apply the plan?",
                    parameters: [text(name: 'Plan', description: 'Please review the plan', defaultValue: plan)]
               }
           }
       }

        stage('Apply') {
            steps {
                bat ('terraform apply -input=false tfplan')
            }
        }
    }

  }
