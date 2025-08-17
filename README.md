# TRL Actions

A collection of reusable GitHub Actions for your organization's development workflow.

## Available Actions

### [Lambda Deploy](./actions/lambda-deploy/)

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
  uses: giftlanding/trl-actions/actions/lambda-deploy@v1.0.0

# Using the main branch (for development/testing)
- name: Deploy to Lambda
  uses: giftlanding/trl-actions/actions/lambda-deploy@main
```

[📖 Full Documentation](./actions/lambda-deploy/README.md)

### [Update Submodules](./actions/update-submodule/)

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

[📖 Full Documentation](./actions/update-submodule/README.md)

## Examples

See the [examples](./examples/) directory for complete workflow examples:

- [Lambda Deploy Example](./examples/lambda-deploy-example.yml) - Complete workflow showing how to use the lambda-deploy action
- [Update Submodules Example](./examples/update-submodule-example.yml) - Complete workflow showing how to use the update-submodule action

## Repository Structure

```
trl-actions/
├── actions/
│   ├── lambda-deploy/          # Lambda deployment action
│   │   ├── action.yml          # Action definition
│   │   └── README.md           # Action documentation
│   └── update-submodule/       # Submodule update action
│       ├── action.yml          # Action definition
│       └── README.md           # Action documentation
├── examples/
│   ├── lambda-deploy-example.yml  # Example usage workflow
│   └── update-submodule-example.yml  # Example usage workflow
├── .github/
│   └── workflows/              # CI/CD for this repository
└── README.md                   # This file
```

## Usage

### Using Actions in Your Repositories

To use any action from this repository in your projects:

```yaml
# Using a specific version tag (recommended for production)
- uses: giftlanding/trl-actions/actions/action-name@v1.0.0

# Using the main branch (for development/testing)
- uses: giftlanding/trl-actions/actions/action-name@main

# Using a specific commit (for testing)
- uses: giftlanding/trl-actions/actions/action-name@abc1234

# With inputs
- uses: giftlanding/trl-actions/actions/action-name@v1.0.0
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
   - uses: giftlanding/trl-actions/actions/lambda-deploy@v1.0.0
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
