# GitHub Actions CI/CD for Pacman

This repository includes comprehensive GitHub Actions workflows for building and packaging Pacman across multiple platforms.

## Available Workflows

### 1. Build Workflow (`build.yml`)
**Trigger**: Push to main/master, pull requests, manual dispatch

This workflow automatically builds Pacman on multiple platforms:
- **Arch Linux** (multiple configurations)
- **Debian/Ubuntu** 
- **Fedora**
- **Standard Ubuntu** (host system)
- **macOS** (latest)

**Features**:
- Matrix builds with different configurations (debug, clang, different crypto backends)
- Automatic testing
- Documentation building
- Artifact uploads for debugging

### 2. Package Workflow (`package.yml`)
**Trigger**: Manual dispatch only

Creates distributable packages:
- **DEB packages** for Debian/Ubuntu
- **RPM packages** for Fedora/RHEL
- **PKG packages** for macOS
- **Source tarballs**
- **GitHub releases**

### 3. Custom Build Workflow (`custom-build.yml`)
**Trigger**: Manual dispatch only

Allows custom builds with user-specified options:
- Choose target OS (Ubuntu, Arch Linux, Debian, Fedora, **macOS**)
- Choose build type (release, debug)
- Enable/disable documentation
- Add custom meson options

## How to Use

### Automatic Builds
Automatic builds trigger on every push to main/master branches and on pull requests. No action needed.

### Manual Package Creation
1. Go to **Actions** tab in your GitHub repository
2. Select **Package Build** workflow
3. Click **Run workflow**
4. Configure options:
   - Package version (or leave as 'auto')
   - Check which packages to create (DEB/RPM/tarball)
5. Click **Run workflow**

### Custom Builds
1. Go to **Actions** tab
2. Select **Custom Package Build** workflow  
3. Click **Run workflow**
3. Configure:
   - **Target OS**: Choose your preferred OS (Ubuntu, Arch Linux, Debian, Fedora, **macOS**)
   - **Build type**: Release or debug
   - **Enable docs**: Whether to build documentation
   - **Custom options**: Additional meson configuration (e.g., `-Dcrypto=nettle`)
5. Click **Run workflow**

## Build Configurations

### Standard Configuration
```bash
meson setup build
ninja -C build
meson test -C build
```

### Common Custom Options
- `-Dcrypto=nettle` - Use Nettle instead of OpenSSL
- `-Dgpgme=disabled` - Disable GPG support
- `-Dcurl=disabled` - Disable cURL support  
- `-Di18n=false` - Disable internationalization
- `--buildtype=debug` - Debug build with symbols
- `-Ddoc=enabled` - Build documentation

### Platform-Specific Notes

#### Arch Linux
- Uses `archlinux:base-devel` container
- Full dependency support
- Preferred platform for Pacman

#### macOS
- Uses `macos-latest` runner
- Homebrew for dependencies
- Creates `.pkg` installer packages
- Includes Homebrew formula

#### Debian/Ubuntu  
- Uses `debian:bookworm` or `ubuntu:22.04`
- Good compatibility
- Creates `.deb` packages

#### Fedora
- Uses `fedora:latest` container
- Creates `.rpm` packages
- Good for RHEL-compatible systems

## Output Artifacts

### Build Artifacts
- **Binaries**: `pacman`, `pacman-conf`, `testpkg`, `vercmp`
- **Libraries**: `libalpm.so`, `libalpm.a`
- **Headers**: Development headers for libalpm
- **Documentation**: Man pages and HTML docs (if enabled)

### Package Artifacts
- **DEB**: `pacman_VERSION_amd64.deb`
- **RPM**: `pacman-VERSION-1.x86_64.rpm`  
- **PKG**: `pacman-VERSION-macos.pkg`
- **Homebrew**: `pacman.rb` (formula file)
- **Source**: `pacman-VERSION-source.tar.gz`

## Local Development

If you want to develop locally after downloading artifacts:

```bash
# Download and extract artifacts from GitHub Actions
unzip pacman-custom-ubuntu-release.zip

# The staging directory contains the installed files
cd staging
ls -la usr/bin/   # Your built binaries
ls -la usr/lib/   # Your built libraries
```

## Troubleshooting

### Build Failures
1. Check the **Actions** tab for failed workflows
2. Download test logs from failed builds
3. Review `meson-logs/` for detailed error information

### Missing Dependencies
The workflows automatically install dependencies, but if you see missing packages:
1. Check the dependency installation step in the workflow
2. Add missing packages to the appropriate installation command

### Permission Issues
GitHub Actions runs in containers with appropriate permissions. Local permission issues won't affect the builds.

## Contributing

To modify the build process:
1. Edit the appropriate workflow file in `.github/workflows/`
2. Test changes by pushing to a branch or running manually
3. Monitor the Actions tab for results

## Supported Platforms

| Platform | Status | Package Format | Notes |
|----------|--------|----------------|-------|
| Arch Linux | ✅ Full | tar.xz | Native platform |
| Ubuntu/Debian | ✅ Full | .deb | Good compatibility |
| Fedora | ✅ Full | .rpm | RHEL compatible |
| macOS | ✅ Full | .pkg, .rb | Homebrew support |

## Environment Variables

The workflows use these environment variables:
- `MAKEFLAGS="-j4"` - Parallel build jobs
- `VERBOSE=1` - Verbose build output  
- `PACKAGE_VERSION` - Version for package builds

## Next Steps

After building:
1. **Test**: Install and test the packages
2. **Deploy**: Use packages in your target environment
3. **Customize**: Modify workflows for your specific needs
4. **Integrate**: Add to your deployment pipeline
