# GitHub Actions Workflows

## Windows Build Workflow

### Overview
The `windows-build.yml` workflow automatically builds the Claudable Electron application for Windows whenever changes are pushed to the `main` or `develop` branches, or when a pull request is opened.

### What it does

1. **Checkout**: Retrieves the latest code from the repository
2. **Setup Node.js**: Installs Node.js v20.x with npm caching for faster builds
3. **Install Dependencies**: Installs all required npm packages using `npm ci`
4. **Generate Prisma Client**: Generates the database client
5. **Linting**: Runs ESLint to check code quality (continues even if there are warnings)
6. **Type Checking**: Runs TypeScript type checking
7. **Build Next.js**: Builds the Next.js web application
8. **Build Electron**: Packages the Electron desktop application for Windows (x64 NSIS installer)
9. **Upload Artifacts**: Saves the Windows executable (.exe) and related files for download

### Triggering the workflow

The workflow runs automatically on:
- Push to `main` or `develop` branches
- Pull requests targeting `main` branch
- Manual trigger via the GitHub Actions UI (workflow_dispatch)

### Manual trigger

To manually trigger the workflow:
1. Go to the "Actions" tab in the GitHub repository
2. Select "Windows Build" from the workflows list
3. Click "Run workflow" button
4. Select the branch you want to build
5. Click "Run workflow"

### Downloading build artifacts

After the workflow completes successfully:
1. Go to the workflow run details page
2. Scroll to the "Artifacts" section at the bottom
3. Download `claudable-windows-x64` (contains the .exe installer)

Artifacts are kept for 30 days.

### Build requirements

- Windows runner (windows-latest)
- Node.js 20.x
- npm 10.x or higher
- Sufficient disk space for Electron build (~2GB)

### Security

- Uses pinned action versions (e.g., `@v4`) for security and stability
- Implements minimal permissions (`contents: read`)
- Uses GitHub's built-in `GITHUB_TOKEN` for authentication
- No secrets are hardcoded in the workflow

### Troubleshooting

If the build fails:
1. Check the workflow logs in the GitHub Actions tab
2. Look for error messages in each step
3. Build logs are automatically uploaded as artifacts on failure
4. Common issues:
   - Type checking errors: Fix TypeScript errors in the code
   - Linting warnings: These don't stop the build (continue-on-error: true)
   - Build failures: Check Next.js or Electron configuration
   - Missing dependencies: Verify package.json is up to date

### Local testing

To test the build locally before pushing:

```bash
# Install dependencies
npm ci

# Generate Prisma client
npm run prisma:generate

# Run linter
npm run lint

# Run type check
npm run type-check

# Build Next.js
npm run build

# Build Electron for Windows (requires Windows OS or Wine)
npm run package:win
```

### Performance optimizations

- npm caching: Speeds up dependency installation
- Uses `npm ci` instead of `npm install` for reproducible builds
- Parallel execution where possible

### Future enhancements

Potential improvements for the workflow:
- Add code signing for Windows executables
- Implement release automation with tags
- Add security scanning (Snyk, Trivy)
- Create multi-platform builds (macOS, Linux)
- Add automated testing before build
