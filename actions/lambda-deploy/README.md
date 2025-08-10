# Lambda Deploy Action

A reusable GitHub Action for building Node.js applications and deploying them to AWS Lambda.

## Features

- 🚀 Automatic Node.js setup with caching
- 📦 Build TypeScript/JavaScript applications
- 🗜️ Smart caching for build artifacts and deployment packages
- 🔐 AWS IAM role-based authentication
- ✅ Deployment verification
- 🎯 Configurable build commands and directories

## Usage

### Versioning

This action can be referenced using different version strategies:

```yaml
# Using a specific version tag (recommended for production)
- uses: giftlanding/trl-actions/actions/lambda-deploy@v1.0.0

# Using the main branch (for development/testing)
- uses: giftlanding/trl-actions/actions/lambda-deploy@main

# Using a specific commit (for testing)
- uses: giftlanding/trl-actions/actions/lambda-deploy@abc1234
```

### Basic Usage

```yaml
name: Deploy Lambda

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Deploy to Lambda
        uses: giftlanding/trl-actions/actions/lambda-deploy@v1.0.0
```

### Advanced Usage

```yaml
name: Deploy Lambda

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Deploy to Lambda
        uses: giftlanding/trl-actions/actions/lambda-deploy@v1.0.0
        with:
          node-version: '20'
          aws-region: 'us-west-2'
          function-name: 'my-custom-function-name'
          build-command: 'npm run build:prod'
          source-dir: 'build'
          package-dir: 'lambda-package'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `node-version` | Node.js version to use | No | `'22'` |
| `aws-region` | AWS region for Lambda deployment | No | `'us-east-1'` |

| `function-name` | Lambda function name (defaults to repository name) | No | Repository name |
| `build-command` | Build command to run | No | `'npm run build'` |
| `source-dir` | Source directory for compiled code | No | `'dist'` |
| `package-dir` | Directory to create deployment package in | No | `'package'` |

## Prerequisites

### AWS IAM Role

The action automatically assumes a role with the pattern: `arn:aws:iam::649006552804:role/{repository-name}-update-code`

For example, if your repository is named `user-service`, the action will assume the role `arn:aws:iam::649006552804:role/user-service-update-code`.

### AWS IAM Role

You need to create an IAM role that the action can assume. The role should have the following permissions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "lambda:UpdateFunctionCode",
        "lambda:GetFunction"
      ],
      "Resource": "arn:aws:lambda:*:*:function:*"
    }
  ]
}
```

### Trust Policy

The role should trust GitHub Actions:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::649006552804:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        },
        "StringLike": {
          "token.actions.githubusercontent.com:sub": "repo:giftlanding/*:*"
        }
      }
    }
  ]
}
```

## Project Structure

Your Node.js project should have:

```
your-lambda-project/
├── src/
│   └── index.ts
├── dist/           # Built files (configurable)
├── package.json
├── package-lock.json
└── tsconfig.json
```

## Caching

The action includes intelligent caching for:

- **Build artifacts**: Caches the compiled output to speed up subsequent builds
- **Deployment packages**: Caches the final zip file to avoid recreating identical packages

## Examples

### TypeScript Project

```yaml
- name: Deploy TypeScript Lambda
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1
  with:
    build-command: 'npm run build'
    source-dir: 'dist'
```

### Custom Build Process

```yaml
- name: Deploy with Custom Build
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1
  with:
    build-command: 'npm run build:lambda'
    source-dir: 'lambda-dist'
```

### Multiple Environments

```yaml
- name: Deploy to Staging
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1
  with:
    function-name: 'my-function-staging'
    aws-region: 'us-east-1'

- name: Deploy to Production
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1
  with:
    function-name: 'my-function-production'
    aws-region: 'us-west-2'
```

## Troubleshooting

### Common Issues

1. **Permission Denied**: Ensure your IAM role has the correct permissions
2. **Function Not Found**: Verify the Lambda function name matches your repository name or specify a custom name
3. **Build Failures**: Check that your build command works locally
4. **Package Size**: Ensure your deployment package doesn't exceed Lambda limits

### Debug Mode

To enable debug output, set the `ACTIONS_STEP_DEBUG` secret to `true` in your repository settings.

## Contributing

This action is part of the `trl-actions` repository. To contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## License

This action is licensed under the MIT License.
