pipeline {
   agent any

   environment {
       AZURE_SUBSCRIPTION_ID = '5bd98cca-b8d1-42c2-bdf9-06d9661efcf2'
       AZURE_TENANT_ID       = '31ff0854-fee3-4c09-b42a-761baf155370'
       RESOURCE_GROUP        = 'myResourceGroup'
       WEBAPP_NAME           = 'myWebApp-NKC-2026'
   }

   stages {
       stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
       }

       stage('Azure Login') {
            steps {
               withCredentials([usernamePassword(
                    credentialsId: 'AzureServicePrincipal',
                    usernameVariable: 'AZURE_CLIENT_ID',
                    passwordVariable: 'AZURE_CLIENT_SECRET'
                )]) {
                    sh '''
                        az login --service-principal \
                          --username $AZURE_CLIENT_ID \
                          --password $AZURE_CLIENT_SECRET \
                          --tenant $AZURE_TENANT_ID
                        
                        az account set --subscription $AZURE_SUBSCRIPTION_ID
                    '''
                }
            }
       }

       stage('Deploy to Azure') {
            steps {
                sh '''
                    # 使用 Python 打包（解决实验环境中 zip 命令缺失的问题）
                    python3 -c "import shutil; shutil.make_archive('app', 'zip', '.')"
                    
                    az webapp deploy \
                      --resource-group $RESOURCE_GROUP \
                      --name $WEBAPP_NAME \
                      --src-path app.zip \
                      --type zip
                '''
            }
       }
   }
}
