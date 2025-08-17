# Update Submodules Action

A reusable GitHub Action for updating Git submodules using a Personal Access Token (PAT) stored in AWS SSM Parameter Store.

## Features

- 🔐 **Secure PAT Management**: Retrieves GitHub PAT from AWS SSM Parameter Store
- 🔑 **Automatic Role Assumption**: Uses the same AWS role pattern as other actions
- 🧹 **Clean Git Configuration**: Automatically cleans up Git config after use
- 🎯 **Flexible Updates**: Can update all submodules or specific ones
- 🔒 **Security**: No PATs stored in repository secrets

## Usage

### Basic Usage

```yaml
name: Update Submodules

on:
  push:
    branches: [main]

jobs:
  update-submodules:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          submodules: false  # We'll update them manually
      
      - name: Update submodules
        uses: giftlanding/trl-actions/actions/update-submodule@main
```

### Advanced Usage

```yaml
- name: Update specific submodule
  uses: giftlanding/trl-actions/actions/update-submodule@main
  with:
    aws-region: 'us-west-2'
    ssm-parameter-path: '/custom/github/pat'
    submodule-path: 'src/shared-lib'
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `aws-region` | AWS region for SSM parameter access | No | `'us-east-1'` |
| `ssm-parameter-path` | SSM parameter path containing the GitHub PAT | No | `'/trl/github/read-schema-pat'` |
| `submodule-path` | Path to specific submodule to update | No | Updates all submodules |

## Prerequisites

### AWS IAM Role

The action automatically assumes a role with the pattern: `arn:aws:iam::649006552804:role/{repository-name}-update-code`

The role needs these permissions:

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

### AWS SSM Parameter

Create a SecureString parameter in AWS SSM:

```bash
aws ssm put-parameter \
  --name "/trl/github/read-schema-pat" \
  --value "ghp_your_github_pat_here" \
  --type "SecureString" \
  --description "GitHub Personal Access Token for submodule access"
```

### GitHub PAT

The PAT should have these permissions:
- `repo` - Full control of private repositories
- `read:packages` - Download packages from GitHub Package Registry (if needed)

## Examples

### Update All Submodules

```yaml
- name: Update all submodules
  uses: giftlanding/trl-actions/actions/update-submodule@main
```

### Update Specific Submodule

```yaml
- name: Update specific submodule
  uses: giftlanding/trl-actions/actions/update-submodule@main
  with:
    submodule-path: 'src/shared-components'
```

### Custom SSM Parameter Path

```yaml
- name: Update with custom PAT parameter
  uses: giftlanding/trl-actions/actions/update-submodule@main
  with:
    ssm-parameter-path: '/my-org/github/pat'
    aws-region: 'us-west-2'
```

### Complete Workflow Example

```yaml
name: Build with Submodules

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      id-token: write
      contents: read
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          submodules: false
      
      - name: Update submodules
        uses: giftlanding/trl-actions/actions/update-submodule@main
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          cache: 'npm'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Build
        run: npm run build
```

## How It Works

1. **Assumes AWS Role**: Uses the same role pattern as other actions
2. **Retrieves PAT**: Reads the GitHub PAT from SSM Parameter Store
3. **Configures Git**: Sets up Git to use the PAT for GitHub access
4. **Updates Submodules**: Runs `git submodule update --init --recursive`
5. **Cleans Up**: Removes the Git configuration to avoid leaving traces

## Security

- ✅ **No PATs in repository**: All tokens stored securely in AWS SSM
- ✅ **Automatic cleanup**: Git configuration is cleaned up after use
- ✅ **Role-based access**: Uses IAM roles for AWS access
- ✅ **Encrypted parameters**: SSM parameters are stored as SecureString

## Troubleshooting

### Common Issues

1. **Permission Denied**: Ensure your IAM role has `ssm:GetParameter` permission
2. **Parameter Not Found**: Verify the SSM parameter path exists and is accessible
3. **Submodule Update Fails**: Check that the PAT has sufficient repository permissions
4. **Git Configuration Error**: Ensure the repository has submodules configured

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
