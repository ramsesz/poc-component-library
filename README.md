# @cubitec/component-library

A Vue 3 component library built as Web Components (Custom Elements), making it framework-agnostic and compatible with Vue 2 applications.

## Installation

### NPM

```bash
npm install @cubitec/component-library
```

### Browser usage (without npm)

You can load the library in the browser without installing via npm. Built files are **not** committed to the repo; they are only attached to [GitHub Releases](https://github.com/YOUR_USERNAME/YOUR_REPO/releases).

#### Option 1: Direct link from GitHub Release

Use the release asset URL (replace `YOUR_USERNAME`, `YOUR_REPO`, and `VERSION`):

```html
<script src="https://github.com/YOUR_USERNAME/YOUR_REPO/releases/download/VERSION/index.umd.js"></script>
```

Example: `https://github.com/cubitec/component-library/releases/download/0.0.1/index.umd.js`

This works as soon as you create a release. No CDN caching; traffic goes through GitHub.

#### Option 2: CDN via npm (jsDelivr / unpkg)

If you **publish this package to npm**, you get a proper CDN (cached, fast) without putting `dist/` in the repo:

```html
<!-- jsDelivr (from npm) -->
<script src="https://cdn.jsdelivr.net/npm/@cubitec/component-library@VERSION/dist/index.umd.js"></script>

<!-- unpkg (from npm) -->
<script src="https://unpkg.com/@cubitec/component-library@VERSION/dist/index.umd.js"></script>
```

Publish with `npm publish` (and match the version to your release tag if you use both).

#### Minimal HTML example

```html
<!DOCTYPE html>
<html>
<head>
  <title>Component Library Example</title>
</head>
<body>
  <cubitec-button label="Click me" variant="primary"></cubitec-button>

  <!-- Use Option 1 (GitHub) or Option 2 (npm CDN) -->
  <script src="https://github.com/YOUR_USERNAME/YOUR_REPO/releases/download/0.0.1/index.umd.js"></script>
  <script>
    document.querySelector('cubitec-button').addEventListener('click', (e) => console.log('Clicked!', e));
  </script>
</body>
</html>
```

#### Publishing a release (GitHub + npm)

1. **npm scope `@cubitec`**  
   The package name is `@cubitec/component-library`. To publish it you must either:
   - **Create an npm organization** named [cubitec](https://www.npmjs.com/org/create) and publish as that org, or  
   - **Use your npm username** as the scope: in `package.json` set `"name": "@YOUR_NPM_USERNAME/component-library"` (then update imports/docs that reference the package name).

2. **Add npm token** (one-time): In the repo go to **Settings → Secrets and variables → Actions**, add a secret `NPM_TOKEN` with an [npm access token](https://www.npmjs.com/settings/~/tokens) (Automation type). The token must be for an account that can publish to the chosen scope (org member or your user). If you see "Access token expired or revoked", create a new token and update the secret.

3. Create a version tag and push; the workflow builds, creates the GitHub release, and publishes to npm:
   ```bash
   git tag 0.0.1
   git push origin 0.0.1
   ```
4. Use the **direct link** from the release (Option 1) or the **CDN** URL from npm (Option 2).

## Usage in Vue 2

### 1. Import and Register Components

In your Vue 2 application's main entry file (e.g., `main.js`):

```javascript
import Vue from 'vue'
import { registerComponents } from '@cubitec/component-library'

// Register all custom elements
registerComponents()

// Your Vue app setup
new Vue({
  // ...
}).$mount('#app')
```

### 2. Configure Vue 2 to Recognize Custom Elements

To avoid Vue 2 warnings about unknown custom elements, configure your Vue 2 app to recognize them.

**Option A: Using vue.config.js (for Vue CLI projects)**

```javascript
// vue.config.js
module.exports = {
  chainWebpack: config => {
    config.module
      .rule('vue')
      .use('vue-loader')
      .tap(options => ({
        ...options,
        compilerOptions: {
          isCustomElement: (tag) => tag.startsWith('cubitec-')
        }
      }))
  }
}
```

**Option B: Global Configuration (for all Vue 2 projects)**

```javascript
// main.js
import Vue from 'vue'

Vue.config.ignoredElements = [
  /^cubitec-/
]
```

### 3. Use Components in Templates

Once registered, you can use the components directly in your Vue 2 templates:

```vue
<template>
  <div>
    <cubitec-button 
      label="Click me" 
      variant="primary"
      @click="handleClick"
    />
    
    <cubitec-button 
      variant="secondary"
      :disabled="true"
    >
      Disabled Button
    </cubitec-button>
  </div>
</template>

<script>
export default {
  methods: {
    handleClick(event) {
      console.log('Button clicked!', event)
    }
  }
}
</script>
```

## Components

### Button

A simple button component with multiple variants.

#### Props

- `label` (string, optional): Button text. If not provided, use slot content.
- `variant` (string, optional): Button style variant. Options: `'primary'` (default) or `'secondary'`.
- `disabled` (boolean, optional): Disable the button. Default: `false`.

#### Events

- `click`: Emitted when the button is clicked (not emitted if disabled).

#### Usage Examples

```vue
<!-- With label prop -->
<cubitec-button label="Submit" variant="primary" />

<!-- With slot content -->
<cubitec-button variant="secondary">
  Cancel
</cubitec-button>

<!-- Disabled button -->
<cubitec-button label="Disabled" :disabled="true" />

<!-- With click handler -->
<cubitec-button 
  label="Click me" 
  @click="handleClick" 
/>
```

## Usage in Vue 3

The library also works in Vue 3 applications:

```javascript
import { createApp } from 'vue'
import { registerComponents } from '@cubitec/component-library'

const app = createApp(App)
registerComponents()
app.mount('#app')
```

## Development

```bash
# Install dependencies
npm install

# Build library
npm run build

# Type checking
npm run type-check

# Run tests
npm test

# Run tests in watch mode
npm test

# Run tests with UI
npm run test:ui

# Run tests once
npm run test:run

# Run tests with coverage
npm run test:coverage

## Test Coverage

Test coverage reports are automatically generated and available in multiple ways:

### Viewing Coverage Reports

1. **GitHub Actions Artifacts** (Recommended)
   - After tests run, coverage HTML reports are uploaded as artifacts
   - Go to the **Actions** tab → Select a workflow run → Download the `coverage-report-20.x` artifact
   - Extract and open `index.html` in your browser for a detailed interactive report

2. **Pull Request Comments**
   - Coverage summary is automatically posted as a comment on pull requests
   - Shows coverage percentages for statements, branches, functions, and lines
   - Includes a link to download the full HTML report

3. **GitHub Actions Summary**
   - Coverage summary is displayed in the workflow run summary
   - View it in the **Actions** tab under the workflow run details

4. **Codecov** (Optional)
   - If you set up a Codecov account and add `CODECOV_TOKEN` secret, coverage is uploaded to codecov.io
   - Provides historical coverage tracking and PR coverage diffs
   - Visit [codecov.io](https://codecov.io) to set up your repository

### Local Coverage Reports

When running `npm run test:coverage` locally, coverage reports are generated in the `coverage/` directory:
- Open `coverage/index.html` in your browser for the interactive HTML report

# Start Storybook
npm run storybook

# Build Storybook
npm run build-storybook
```

## Storybook

This project includes [Storybook](https://storybook.js.org/) for component development and documentation. You can view and interact with all components in isolation.

### Local Development

```bash
# Start Storybook development server
npm run storybook

# Build static Storybook site
npm run build-storybook
```

The Storybook will be available at `http://localhost:6006` when running the dev server.

### Public Deployment

Storybook is automatically deployed to GitHub Pages on every push to the `main` branch. The public Storybook is available at:

**https://YOUR_USERNAME.github.io/YOUR_REPO_NAME/**

Replace `YOUR_USERNAME` and `YOUR_REPO_NAME` with your actual GitHub repository details.

The deployment is handled automatically by the GitHub Actions workflow (`.github/workflows/storybook.yml`). To enable GitHub Pages:

1. Go to your repository settings on GitHub
2. Navigate to **Pages** in the left sidebar
3. Under **Source**, select **GitHub Actions**
4. The workflow will automatically deploy Storybook on every push to `main`

You can also manually trigger the deployment by going to the **Actions** tab and running the "Deploy Storybook to GitHub Pages" workflow.

## Building

The library is built using Vite and outputs:

- **ESM format**: `dist/index.esm.js` (for modern bundlers)
- **UMD format**: `dist/index.umd.js` (for direct browser use)
- **Type definitions**: `dist/index.d.ts`

## License

MIT
