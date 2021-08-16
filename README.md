# Fire Layer

This repo produces a AWS Layer which includes the baseline of requirements that'll need in AWS to use apps leveraging Firemodel. The top level dependencies which this layer will provide includes:

- Firemodel
- Universal Fire
- firebase-admin (from google)
- Firemock
- Faker

Your Lambda functions which include this layer will find these top level dependencies along with their dependency graphs are what you will find in this layer in the `/opt/node_modules` filesystem. In order for your functions import statements to properly resolve this path you will need to ensure that the `NODE_PATH` ENV variable is set to include the mount directory above. 

### Serverless Framework

If you are using the serverless framework, you'd simply add something like the following to your `serverless.yaml` file:

```yaml
functions:
    MyFunction:
        layers:
            - 'arn-path-to-this-layer'
        environment:
            NODE_PATH: "./node_modules:/opt/node_modules"
```

alternatively if you're using this layer in many places, or have other layers which drop NPM deps into `opt/node_modules` then you can just add it to your global environment:

```yaml
provider:
    name: aws
    runtime: nodejs14.x
    environment:
        NODE_PATH: "./node_modules:/opt/node_modules"
```

You're actual config will clearly vary based on your needs but a common strategy is to have a `env.yaml` where you'll put all your non-secret ENV variables and then refer to this file in your master `serverless.yaml` like so:

```yaml
provider:
    environment: '${file(serverless-config/env.yml):${self:custom.stage}}'
```

This then allows you configure your `env.yml` with per-stage as well as global ENV variables and tends to work much better than trying to fit it all into the `serverless.yml`. Here's an example of what it might look like:

```yaml
global: &all_stages
    MY_SILLY_API: "xxyyzz234324"

dev:
    <<: *all_stages
    FIREBASE_DATABASE_URL: "https://test.firebase.com"

prod:
    <<: *all_stages
    FIREBASE_DATABASE_URL: "https://prod.firebase.com"
```

## Deployment

You can either reference the ARN of this layer's formal deployment or just deploy it locally into your own account. To deploy it to your own account:

```sh
# install local dev-deps
yarn
# builds layer and deploys using serverless framework
yarn deploy
```

Otherwise you can just refer to the following ARNs:

- 0.58 
    - **description:** the latest versions of Firemodel and Universal Fire on the 0.58.x versions; this includes the latest `firebase-admin` (9.5.x)
    - **ARN**: 


