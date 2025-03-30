# EC2 ドッカーコンテナのアップデート

## AWSへの質問

AWS上にクラウドサービスを構築します。
EC2 で ドッカーコンテナを使用しています。
機能をアップデートするため、新しいコンテナを作成し、EC2にデプロイする必要があります。
リポジトリはGitHub を使用します。GitHubの特定のブランチを更新した場合に、EC2に自動デプロイさせたいです。


To automatically deploy Docker containers to EC2 when a specific branch on GitHub is updated, you can set up a continuous integration and deployment (CI/CD) pipeline using AWS CodePipeline and AWS CodeBuild. Here's an overview of the process and the steps to implement it:

1. **Set up AWS CodePipeline**
   Create a pipeline in AWS CodePipeline that will orchestrate the entire deployment process. [[1]](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-ecs-ecr-codedeploy.html)
   - Go to the AWS CodePipeline console (https://console.aws.amazon.com/codesuite/codepipeline/home)
   - Click "Create pipeline" and follow the wizard to set up your pipeline

2. **Configure Source Stage**
   Connect your GitHub repository to the pipeline.
   - In the Source stage, select GitHub as the source provider
   - Connect your GitHub account and select the repository and branch you want to monitor
   - Set up a webhook to automatically trigger the pipeline when changes are pushed to the specified branch

3. **Set up AWS CodeBuild**
   Create a build project that will build your Docker image and push it to Amazon ECR.
   - Go to the AWS CodeBuild console (https://console.aws.amazon.com/codesuite/codebuild/home)
   - Click "Create build project" and configure it to use your GitHub repository as the source
   - Specify a Docker-capable environment for your build [[2]](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/deploying-using-github.html)
   - Create a buildspec.yml file in your repository with instructions to build and push your Docker image

4. **Add Build Stage to Pipeline**
   Include the CodeBuild project in your CodePipeline.
   - In your pipeline, add a Build stage
   - Select AWS CodeBuild as the build provider and choose your CodeBuild project

5. **Create EC2 Deployment Configuration**
   Prepare your EC2 instance to receive and run the updated Docker container.
   - Set up your EC2 instance with the necessary permissions to pull images from ECR
   - Install and configure the AWS CLI and Docker on your EC2 instance
   - Create a script on your EC2 instance to pull and run the latest Docker image

6. **Add Deployment Stage to Pipeline**
   Configure the pipeline to deploy the new container to your EC2 instance.
   - Add a Deploy stage to your pipeline
   - Use AWS CodeDeploy or AWS Systems Manager to execute the deployment script on your EC2 instance

7. **Test and Monitor**
   Ensure your pipeline is working correctly and set up monitoring.
   - Make a change to your specified GitHub branch to trigger the pipeline [[3]](https://docs.aws.amazon.com/app2container/latest/UserGuide/a2c-integrations-apprunner.html)
   - Monitor the pipeline execution in the CodePipeline console
   - Set up CloudWatch alarms to notify you of any pipeline failures

By completing these steps, you'll have created an automated deployment pipeline that builds and deploys your Docker container to EC2 whenever changes are pushed to your specified GitHub branch. This setup improves your development workflow by ensuring that your EC2 instance always runs the latest version of your application.

Remember to follow AWS security best practices throughout this process, such as using IAM roles with least privilege, securing your EC2 instance, and properly managing your Docker images in ECR. Always test your pipeline thoroughly in a non-production environment before implementing it for your production workloads.
Sources
[1] [[1] Tutorial: Create a pipeline with an Amazon ECR source and ECS-to-CodeDeploy deployment - AWS CodePipeline] (https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-ecs-ecr-codedeploy.html)
[3] [[2] Using GitHub Actions to deploy with AWS SAM - AWS Serverless Application Model] (https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/deploying-using-github.html)
[6] [[3] Deploy application containers to AWS App Runner with AWS App2Container - AWS App2Container] (https://docs.aws.amazon.com/app2container/latest/UserGuide/a2c-integrations-apprunner.html)


GitHub 上の特定のブランチが更新されたときに Docker コンテナを EC2 に自動的にデプロイするには、AWS CodePipeline と AWS CodeBuild を使用して継続的インテグレーションとデプロイメント (CI/CD) パイプラインを設定できます。プロセスの概要と実装手順は次のとおりです。

1. **AWS CodePipeline をセットアップする**
AWS CodePipeline で、デプロイメントプロセス全体をオーケストレーションするパイプラインを作成します。[[1]](https://docs.aws.amazon.com/codepipeline/latest/userguide/tutorials-ecs-ecr-codedeploy.html)
- AWS CodePipeline コンソール (https://console.aws.amazon.com/codesuite/codepipeline/home) に移動します
- [パイプラインの作成] をクリックし、ウィザードに従ってパイプラインをセットアップします

2. **ソース ステージを構成する**
GitHub リポジトリをパイプラインに接続します。
- ソース ステージで、ソース プロバイダーとして GitHub を選択します
- GitHub アカウントを接続し、監視するリポジトリとブランチを選択します
- 指定したブランチに変更がプッシュされたときにパイプラインを自動的にトリガーする Webhook をセットアップします

3. **AWS CodeBuild をセットアップする**
Docker イメージをビルドして Amazon ECR にプッシュするビルド プロジェクトを作成します。
- AWS CodeBuild コンソール (https://console.aws.amazon.com/codesuite/codebuild/home) に移動します
- 「ビルド プロジェクトの作成」をクリックし、GitHub リポジトリをソースとして使用するように設定します
- ビルド用の Docker 対応環境を指定します [[2]](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/deploying-using-github.html)
- Docker イメージをビルドしてプッシュするための手順を記載した buildspec.yml ファイルをリポジトリに作成します

4. **パイプラインにビルドステージを追加**
CodePipeline に CodeBuild プロジェクトを含めます。
- パイプラインにビルドステージを追加します
- ビルドプロバイダーとして AWS CodeBuild を選択し、CodeBuild プロジェクトを選択します

5. **EC2 デプロイ構成を作成します**
更新された Docker コンテナを受け取って実行できるように EC2 インスタンスを準備します。
- ECR からイメージをプルするために必要な権限で EC2 インスタンスを設定します
- EC2 インスタンスに AWS CLI と Docker をインストールして設定します
- EC2 インスタンスに最新の Docker イメージをプルして実行するためのスクリプトを作成します

6. **パイプラインにデプロイステージを追加します**
EC2 インスタンスに新しいコンテナをデプロイするようにパイプラインを設定します。
- パイプラインにデプロイステージを追加します
- AWS CodeDeploy または AWS Systems Manager を使用して、EC2 インスタンスでデプロイスクリプトを実行します

7. **テストと監視**
パイプラインが正しく動作していることを確認し、監視を設定します。
- 指定した GitHub ブランチに変更を加えてパイプラインをトリガーします [[3]](https://docs.aws.amazon.com/app2container/latest/UserGuide/a2c-integrations-apprunner.html)
- CodePipeline コンソールでパイプラインの実行を監視します
- パイプラインの障害を通知する CloudWatch アラームを設定します