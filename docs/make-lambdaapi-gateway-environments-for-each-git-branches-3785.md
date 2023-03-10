# 为每个 git 分支创建 Lambda/API 网关环境

> 原文：<https://dev.to/kawahara/make-lambdaapi-gateway-environments-for-each-git-branches-3785>

为了测试方便，我们的团队尝试为每个 git 分支创建 Lambda/API 网关环境。

## 背景/需求

*   我们使用 CircleCI 进行测试和部署。
*   使用[无服务器](https://serverless.com/)和[无服务器快速](https://github.com/awslabs/aws-serverless-express)创建的 Web 应用程序。
*   您的域名由 Route53 管理，SSL 证书由亚马逊证书管理器在 **us-east-1** 地区颁发

## 安装插件

要管理自定义域，请安装插件:

```
yarn add -D serverless-domain-manager 
```

Enter fullscreen mode Exit fullscreen mode

## 配置 serverless.yml

*   为了避免为每个分支创建 S3 存储桶，将`deploymentBucket`设置为`serverless.yml`以指定存储桶位置。
*   为了切换环境，serverless.yml 从`./config/sls-config.yml`加载配置。(如果需要设置安全变量，使用 KMS 对其进行加密。)

```
# serverless.yml

service:
  name: ${self:custom.name}

provider:
  name: aws
  region: ap-northeast-1
  runtime: nodejs6.10
  stage: ${opt:stage, 'dev'}
  deploymentBucket:
    name: ${self:custom.name}.${self:provider.region}.deploy
  environment:
    XXX: ${self:custom.config.XXX, ''}
    YYY: ${self:custom.config.YYY, ''}

custom:
  name: project-name
  customDomain:
    domainName: ${self:provider.stage}.sls.example.com
    basePath: ''
    stage: ${self:provider.stage}
    certificateName: '*.sls.example.com'
    createRoute53Record: true
  config: ${file(./config/sls-config.yml)}

plugins:
  - serverless-domain-manager

functions:
  render:
    handler: server.render
    events:
      - http:
          path: '/'
          method: 'get'
          private: false
      - http:
          path: '{proxy+}'
          method: 'get'
          private: false 
```

Enter fullscreen mode Exit fullscreen mode

## 对每个环境进行配置

制作分支布局。举个例子，

*   `master` branch =针对生产环境。
*   `develop` branch =针对开发环境。
*   其他分支=预览环境。

并为每个环境创建配置文件。并将对象放入带有环境前缀的任何 S3 存储桶:

*   产量=> `s3://config-bucket/production/sls-config.yml`
*   发展=> `s3://config-bucket/development/sls-config.yml`
*   预览=> `s3://config-bucket/preview/sls-config.yml`

## 制作部署脚本

*   确保不能使用`-`、`_`来 API Gateway 阶段名或 CloudFormation 模板名。所以这个脚本从 git 分支名称中删除了这些字符。(例如`123-test-branch` = > `123testbranch`)它将用于无服务器框架的阶段名。
*   将脚本文件权限设置为 755。

```
#!/bin/bash

# scripts/deploy.sh

set -eu

export NODE_ENV="preview"

if [ "${CIRCLE_BRANCH}" == "master" ]; then export NODE_ENV="production"
elif [ "${CIRCLE_BRANCH}" == "develop" ]; then export NODE_ENV="development"
fi STAGE=${CIRCLE_BRANCH//[-\/]/}
echo stage=${STAGE}

# get config file from S3
aws s3 cp s3://config-bucket/${NODE_ENV}/sls-config.yml config/

# make Route53 configuration
./node_modules/.bin/sls create_domain --stage=$STAGE
# deploy
./node_modules/.bin/sls deploy --stage=$STAGE

# remove old CloudFormation stacks when you delete remote branch
STACKS=`aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE CREATE_FAILED UPDATE_COMPLETE ROLLBACK_COMPLETE ROLLBACK_FAILED UPDATE_ROLLBACK_FAILED --max-items 1000 | jq '.StackSummaries[].StackName' -r | grep '^project-name-'`
BRANCHES=`git branch -r | sed -e "s/^[[:space:]]*origin\///" | grep -v "^HEAD" | uniq`

echo "stacks----"
echo "${STACKS}"
echo "branches----"
echo "${BRANCHES}"

while read line
do found=0

    lineStage=`echo $line | sed -e 's/^project-name-//'`

    while read branch
    do stage=${branch//[-\/]/}

        if [[ "$lineStage" = "$stage" ]]; then found=1
            break;
        fi
    done <<END  $BRANCHES  END

    if [ $found -eq 0 ]; then
      ./node_modules/.bin/sls remove --stage=$lineStage || true
      ./node_modules/.bin/sls delete_domain --stage=$lineStage || true fi

done <<END  $STACKS  END 
```

Enter fullscreen mode Exit fullscreen mode

## 配置`.circleci/config.yml`

您的部署 docker 映像需要以下内容:

*   节点 js6
*   大蟒
*   点
*   japan quarterly 日本季刊
*   awscil

并使`.circleci/config.yml`

```
# .circleci/config.yml

version: 2
jobs:
  build:
    docker:
      - image: my_docker_image
    parallelism: 1
    working_directory: ~/project-name
    steps:
      - checkout

      - restore_cache:
          key: project-name-yarn-{{ checksum "yarn.lock" }}

      - run:
          name: Install node modules
          command: yarn install

      - run:
          name: lint
          command: yarn run lint

      - run:
          name: unit
          command: yarn run unit

      - save_cache:
          key: project-name-yarn-{{ checksum "yarn.lock" }}
          paths:
            - ~/.yarn-cache

      - run:
          name: deploy
          command: scripts/deploy.sh 
```

Enter fullscreen mode Exit fullscreen mode

## 搞定！

通过这种配置，CircleCI 将使 Lambda/API 网关环境达到`${stagename}.sls.example.com`。首次映射域时，Route53 & CloudFront 需要 40 分钟。