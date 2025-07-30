# OCI v1 to Docker v2 Schema 2 Conversion Workflow

## Overview

This GitHub Actions workflow converts OCI v1 format container images to Docker v2 Schema 2 format using Skopeo. This is useful for ensuring compatibility with tools that don't support OCI v1 format.

## Workflow: `convert-oci-to-docker-v2.yml`

### Purpose

Converts container images from OCI v1 format (`application/vnd.oci.image.manifest.v1+json`) to Docker v2 Schema 2 format (`application/vnd.docker.distribution.manifest.v2+json`) in a Nexus registry.

### Trigger

- **Manual trigger** via `workflow_dispatch`
- Can be run from the GitHub Actions tab in the repository

### Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `source_image` | Source image name (repository:tag) | No | `ubi9/ubi:latest` |
| `target_image` | Target image name (repository:tag) | No | `ubi9/ubi-converted:v2s2` |

### Required Secrets

The workflow requires the following GitHub repository secrets:

| Secret | Description |
|--------|-------------|
| `NEXUS_USERNAME` | Username for Nexus registry authentication |
| `NEXUS_PASSWORD` | Password for Nexus registry authentication |

### Configuration

The workflow is configured for the Nexus registry at `nexusrepository.bradesco.com.br:8500`. To use with a different registry, update the `NEXUS_REGISTRY` environment variable in the workflow file.

### Features

1. **Image Format Verification**: Validates that the source image is in OCI v1 format
2. **Skopeo Conversion**: Uses Skopeo with `--format v2s2` to convert the image
3. **Post-Conversion Validation**: Confirms the target image is in Docker v2 Schema 2 format
4. **Functionality Verification**: Compares layer counts to ensure image integrity
5. **Security**: Uses secure credential handling and TLS verification options
6. **Detailed Logging**: Provides comprehensive output and summary

### Workflow Steps

1. **Setup**: Install and verify Skopeo
2. **Variable Configuration**: Set source and target image paths
3. **Source Verification**: Check source image format is OCI v1
4. **Conversion**: Convert image using Skopeo with v2s2 format
5. **Target Verification**: Confirm target image is Docker v2 Schema 2
6. **Functionality Check**: Compare image metadata and layer counts
7. **Summary Generation**: Create detailed conversion report

### Usage

1. **Set up secrets** in your GitHub repository:
   - Go to Settings → Secrets and variables → Actions
   - Add `NEXUS_USERNAME` and `NEXUS_PASSWORD`

2. **Run the workflow**:
   - Go to Actions tab in your repository
   - Select "Convert OCI v1 to Docker v2 Schema 2" workflow
   - Click "Run workflow"
   - Optionally specify custom source and target image names
   - Click "Run workflow" to start

3. **Monitor progress**:
   - Watch the workflow execution in real-time
   - Review the detailed summary generated at the end
   - Check the converted image in your Nexus registry

### Example Usage

**Default conversion:**
- Source: `nexusrepository.bradesco.com.br:8500/ubi9/ubi:latest`
- Target: `nexusrepository.bradesco.com.br:8500/ubi9/ubi-converted:v2s2`

**Custom conversion:**
- Source: `nexusrepository.bradesco.com.br:8500/myapp/app:v1.0`
- Target: `nexusrepository.bradesco.com.br:8500/myapp/app-docker:v1.0`

### Error Handling

The workflow includes comprehensive error handling:

- **Authentication failures**: Clear error messages for credential issues
- **Format verification**: Fails if source is not OCI v1 or target is not Docker v2 Schema 2
- **Conversion failures**: Detailed Skopeo error output
- **Verification failures**: Layer count and metadata comparison failures

### Security Considerations

- Credentials are handled securely via GitHub Secrets
- TLS verification can be disabled for internal registries (`--src-tls-verify=false`, `--dest-tls-verify=false`)
- No credentials are logged or exposed in workflow output

### Troubleshooting

**Common issues:**

1. **Authentication failures**: Verify `NEXUS_USERNAME` and `NEXUS_PASSWORD` secrets are correctly set
2. **Source image not found**: Check the source image name and registry accessibility
3. **Format verification failures**: Ensure the source image is actually in OCI v1 format
4. **Network issues**: Verify registry accessibility from GitHub Actions runners

**Debug tips:**

- Check the "Verify source image format" step for source image details
- Review the conversion step for Skopeo error messages
- Examine the verification steps for format confirmation
- Use the generated summary for overall conversion status

### Registry Configuration

To use this workflow with a different registry:

1. Update the `NEXUS_REGISTRY` environment variable in the workflow file
2. Update the credential secret names if needed
3. Adjust TLS verification settings based on your registry configuration

### Dependencies

- **Skopeo**: Installed via Ubuntu package manager
- **jq**: Pre-installed on GitHub Actions Ubuntu runners
- **Python**: Pre-installed, used for YAML validation during development