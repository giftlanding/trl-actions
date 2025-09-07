# TRL Actions

A collection of reusable GitHub Actions for your organization's development workflow.

## Available Actions

## Available Actions

### Lambda Deploy Action

Build Node.js applications and deploy them to AWS Lambda with intelligent caching and verification.

**Features:**
- 🚀 Automatic Node.js setup with caching
- 📦 Build TypeScript/JavaScript applications
- 🗜️ Smart caching for build artifacts and deployment packages
- 🔐 AWS IAM role-based authentication
- ✅ Deployment verification

**Quick Start:**
```yaml
# Using a specific version tag (recommended for production)
- name: Deploy to Lambda
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1.5.4

# Using the main branch (for development/testing)
- name: Deploy to Lambda
  uses: giftlanding/trl-actions/actions/lambda-deploy@main
```

**Inputs:**
| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `node-version` | Node.js version to use | No | `'22'` |
| `aws-region` | AWS region for Lambda deployment | No | `'us-east-1'` |
| `function-name` | Lambda function name (defaults to repository name) | No | Repository name |
| `build-command` | Build command to run | No | `'npm run build'` |
| `source-dir` | Source directory for compiled code | No | `'dist'` |
| `package-dir` | Directory to create deployment package in | No | `'package'` |
| `cache-key-patterns` | File patterns to include in cache key (comma-separated) | No | `'src/**/*.ts,src/**/*.js,src/**/*.json,tsconfig.json,webpack.config.js'` |
| `force-rebuild` | Force rebuild by invalidating cache | No | `'false'` |
| `arm-native` | Build native ARM64 binaries using Docker | No | `'false'` |

**Advanced Usage:**
```yaml
- name: Deploy to Lambda
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1.5.4
  with:
    node-version: '20'
    aws-region: 'us-west-2'
    function-name: 'my-custom-function-name'
    build-command: 'npm run build:prod'
    source-dir: 'build'
    package-dir: 'lambda-package'
    cache-key-patterns: 'src/**/*.ts,src/**/*.js,src/**/*.json,tsconfig.json,webpack.config.js,config/**/*.js'
    force-rebuild: 'false'
```

**Cache Invalidation:**
```yaml
# Force a complete rebuild (useful for debugging cache issues)
- name: Deploy to Lambda with Force Rebuild
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1.5.4
  with:
    force-rebuild: 'true'
```

**Docker Build for Native ARM64 Binaries:**
```yaml
- name: Deploy to Lambda with Docker
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1.5.4
  with:
    arm-native: 'true'
    node-version: '22'
```

**How it Works:**
The action automatically creates a Dockerfile that:
- Uses the AWS Lambda Node.js base image
- Installs dependencies with ARM64 native binaries
- Runs your build command inside the Lambda environment
- Creates the deployment package with native ARM64 modules
- Extracts the function.zip for deployment

**Build Process Assumptions:**

The lambda-deploy action assumes:

1. **Node.js Project**: 
   - Has `package.json` and `package-lock.json` files
   - Uses `npm ci` for dependency installation

2. **Build Output**: 
   - Build process creates files in a single output directory
   - Output directory contains the final runtime code (no further transpilation needed)

3. **Dependencies**: 
   - All runtime dependencies are in `package.json`
   - No native dependencies that require compilation (unless using Docker)

**Docker Build Assumptions (when arm-native=true):**
- Action automatically creates a Dockerfile for ARM64 builds
- Uses AWS Lambda base image (e.g., `public.ecr.aws/lambda/nodejs:22`)
- Builds the project inside the ARM64 Lambda environment
- Creates `/var/task/function.zip` in the container
- **Automatically detects and rebuilds native dependencies** for ARM64 platform
- **Configures webpack externals** for native dependencies to prevent bundling
- **Sets proper environment variables** for cross-platform builds
- **Verifies native dependency installation** in the final package

**Compatible Build Tools:**
- ✅ TypeScript (`tsc`)
- ✅ Webpack (with proper output configuration)
- ✅ Rollup
- ✅ esbuild
- ✅ Babel (with proper output)

**Incompatible Build Processes:**
- ❌ Serverless Framework (use `serverless deploy` instead)
- ❌ SAM (use `sam build && sam deploy` instead)
- ❌ Multi-package monorepos (without custom configuration)
- ❌ Builds that create multiple output directories

**Native Dependencies Support:**
- ✅ **ARM64 native dependencies** are fully supported when `arm-native=true`
- ✅ **Automatic detection** of native dependencies (Sharp, canvas, etc.)
- ✅ **Proper rebuilding** for ARM64 platform
- ✅ **Webpack externals configuration** to prevent bundling issues
- ✅ **Verification** of native dependency installation

### Update Submodules Action

Update Git submodules using a Personal Access Token stored securely in AWS SSM Parameter Store.

**Features:**
- 🔐 Secure PAT management via AWS SSM
- 🔑 Automatic AWS role assumption
- 🧹 Clean Git configuration management
- 🎯 Flexible submodule updates
- 🔒 No PATs stored in repository secrets

**Quick Start:**
```yaml
# Using a specific version tag (recommended for production)
- name: Update submodules
  uses: giftlanding/trl-actions/actions/update-submodule@v1.1.0

# Using the main branch (for development/testing)
- name: Update submodules
  uses: giftlanding/trl-actions/actions/update-submodule@main
```

**Inputs:**
| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `aws-region` | AWS region for SSM parameter access | No | `'us-east-1'` |
| `ssm-parameter-path` | SSM parameter path containing the GitHub PAT | No | `'/trl/github/read-schema-pat'` |
| `submodule-path` | Path to specific submodule to update | No | Updates all submodules |

**Advanced Usage:**
```yaml
- name: Update specific submodule
  uses: giftlanding/trl-actions/actions/update-submodule@v1.1.0
  with:
    aws-region: 'us-west-2'
    ssm-parameter-path: '/custom/github/pat'
    submodule-path: 'src/shared-lib'
```

### Upload Documentation Action

Upload documentation files to S3 bucket with automatic file detection and proper content types.

**Features:**
- 🔍 Automatic documentation file detection
- 📁 Organized S3 structure by repository name
- 🏷️ Proper content type detection
- 🔑 Automatic AWS role assumption
- 📊 Metadata tracking with repository and commit info

**Quick Start:**
```yaml
- name: Upload documentation
  uses: giftlanding/trl-actions/actions/upload-docs@v1.3.0
  with:
    s3-bucket: 'trl-storage'
```

**Inputs:**
| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `aws-region` | AWS region for S3 access | No | `'us-east-1'` |
| `s3-bucket` | S3 bucket name to upload documentation to | No | `'trl-storage'` |
| `docs-files` | Comma-separated list of documentation files to check and upload | No | `'README.md,CHANGELOG.md,API.md,swagger.yml,docs/**,*.md'` |

**Advanced Usage:**
```yaml
- name: Upload custom documentation files
  uses: giftlanding/trl-actions/actions/upload-docs@v1.3.0
  with:
    s3-bucket: 'trl-storage'
    aws-region: 'us-west-2'
    docs-files: 'README.md,API.md,docs/**,*.pdf'
```

## Prerequisites

### AWS IAM Role

Both actions automatically assume a role with the pattern: `arn:aws:iam::649006552804:role/{repository-name}-update-code`

For example, if your repository is named `user-service`, the actions will assume the role `arn:aws:iam::649006552804:role/user-service-update-code`.

#### Required Permissions

**For Lambda Deploy Action:**
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

**For Update Submodules Action:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ssm:GetParameter"
      ],
      "Resource": "arn:aws:ssm:*:*:parameter/trl/github/*"
    }
  ]
}
```

**For Upload Documentation Action:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:PutObjectAcl"
      ],
      "Resource": "arn:aws:s3:::your-docs-bucket/docs/*"
    }
  ]
}
```

#### Trust Policy

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

### AWS SSM Parameter (Update Submodules Action)

Create a SecureString parameter in AWS SSM for the GitHub PAT:

```bash
aws ssm put-parameter \
  --name "/trl/github/read-schema-pat" \
  --value "ghp_your_github_pat_here" \
  --type "SecureString" \
  --description "GitHub Personal Access Token for submodule access"
```

The PAT should have these permissions:
- `repo` - Full control of private repositories
- `read:packages` - Download packages from GitHub Package Registry (if needed)

## Troubleshooting

### Common Lambda Deploy Issues

**Build Fails:**
- Ensure your build command works locally: `npm run build`
- Check that the source directory exists after build
- Verify TypeScript compilation succeeds

**Package Too Large:**
- Use `.npmignore` to exclude unnecessary files
- Consider using `npm ci --omit=dev` in your build process
- Check for large dependencies that could be dev dependencies

**Missing Dependencies:**
- Ensure all runtime dependencies are in `package.json` (not devDependencies)
- Check that native dependencies are compatible with Lambda runtime
- Verify `package-lock.json` is up to date

**Wrong Entry Point:**
- Ensure your Lambda function entry point matches the compiled output
- Check that `index.js` (or your entry file) exists in the source directory
- Verify the handler path in your Lambda configuration

**Cache Issues (Stale Deployments):**
- **Symptoms**: Deployed code doesn't match recent commits, missing features, old behavior
- **Debug**: Check the "Debug cache key" step output in your workflow logs
- **Solution**: Use `force-rebuild: 'true'` to invalidate all caches
- **Prevention**: Ensure `cache-key-patterns` includes all files that affect your build:
  ```yaml
  cache-key-patterns: 'src/**/*.ts,src/**/*.js,src/**/*.json,tsconfig.json,webpack.config.js,config/**/*.js'
  ```
- **Common Missing Patterns**: `tsconfig.json`, `webpack.config.js`, config files, environment files

### Common Submodule Issues

**Permission Denied:**
- Ensure your IAM role has `ssm:GetParameter` permission
- Verify the SSM parameter path exists and is accessible
- Check that the PAT has sufficient repository permissions

**Submodule Update Fails:**
- Verify the repository has submodules configured
- Check that the PAT has access to all submodule repositories
- Ensure submodule paths are correct

## Examples

See the [examples](./examples/) directory for complete workflow examples:

- [Lambda Deploy Example](./examples/lambda-deploy-example.yml) - Complete workflow showing how to use the lambda-deploy action
- [Update Submodules Example](./examples/update-submodule-example.yml) - Complete workflow showing how to use the update-submodule action
- [Upload Documentation Example](./examples/upload-docs-example.yml) - Complete workflow showing how to use the upload-docs action

## Repository Structure

```
trl-actions/
├── actions/
│   ├── lambda-deploy/          # Lambda deployment action
│   │   └── action.yml          # Action definition
│   ├── update-submodule/       # Submodule update action
│   │   └── action.yml          # Action definition
│   └── upload-docs/            # Documentation upload action
│       └── action.yml          # Action definition
├── examples/
│   ├── lambda-deploy-example.yml  # Example usage workflow
│   ├── update-submodule-example.yml  # Example usage workflow
│   └── upload-docs-example.yml  # Example usage workflow
├── .github/
│   └── workflows/              # CI/CD for this repository
└── README.md                   # This file (contains all documentation)
```

## Usage

### Using Actions in Your Repositories

To use any action from this repository in your projects:

```yaml
# Using a specific version tag (recommended for production)
- uses: giftlanding/trl-actions/actions/action-name@v1.1.0

# Using the main branch (for development/testing)
- uses: giftlanding/trl-actions/actions/action-name@main

# Using a specific commit (for testing)
- uses: giftlanding/trl-actions/actions/action-name@abc1234

# With inputs
- uses: giftlanding/trl-actions/actions/action-name@v1.1.0
  with:
    input-name: value
```

### Versioning

Actions are versioned using:
- **Tags**: `v1.0.0`, `v1.1.0`, `v2.0.0` for stable releases
- **Branches**: `main` for latest development version
- **Commits**: Specific commit hashes for testing

**Note**: All actions in this repository share the same version tags. When you create a tag like `v1.0.0`, all actions become available at that version.

## Creating Releases

To create a new version of your actions:

1. **Create a tag**:
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```

2. **Create a GitHub Release** (optional but recommended):
   - Go to your repository on GitHub
   - Click "Releases" → "Create a new release"
   - Choose the tag you just created
   - Add release notes describing changes

3. **All actions become available** at the new version:
   ```yaml
   - uses: giftlanding/trl-actions/actions/lambda-deploy@v1.5.4
   - uses: giftlanding/trl-actions/actions/ecs-deploy@v1.0.0  # Future action
   ```

## Contributing

### Adding New Actions

1. Create a new directory under `actions/`
2. Add an `action.yml` file defining the action
3. Create comprehensive documentation in `README.md`
4. Add tests and examples
5. Update this main README

### Action Development Guidelines

- Use composite actions for simple workflows
- Provide clear, comprehensive documentation
- Include examples for common use cases
- Add proper error handling and validation
- Follow security best practices
- Include caching where appropriate

### Testing Actions

Each action should include:
- Unit tests for any custom logic
- Integration tests for the full workflow
- Example workflows in the documentation

## Security

- All actions use OIDC for AWS authentication
- No secrets are stored in action code
- Actions follow the principle of least privilege
- Regular security audits are performed

## Support

For issues, questions, or contributions:
1. Check the action-specific documentation
2. Search existing issues
3. Create a new issue with detailed information
4. For urgent matters, contact the DevOps team

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
