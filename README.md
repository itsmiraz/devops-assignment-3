## Prerequisites

- Node Version 22

### 1. For Run This Applications

```bash
# install packages
npm install

# Testing The Applications
npm run check

# For Run the application
npm start
```

## CI/CD — Challenges & Solutions

### 1) Action reference typos

**Symptom:** Workflow failed with “Unable to resolve action … repository not found.”  
**Root cause:** Mis-typed action names/owners (e.g., `action/upload-artifact` instead of `actions/upload-artifact`).  
**Solution:** Corrected to the latest official actions:

- `actions/checkout@v4`
- `actions/setup-node@v4`
- `actions/upload-artifact@v4`
- `actions/download-artifact@v4`  
  **Evidence:** Subsequent runs pulled actions successfully and the `test` job uploaded the artifact.

### 2) Artifact download timing out on self-hosted runner (DNS)

**Symptom:** `actions/download-artifact` failed with `ETIMEDOUT` while listing artifacts.  
**Root cause:** DNS resolution for `actions.githubusercontent.com` was failing on the original WSL-based runner.  
**Solution:** Switched to a Windows self-hosted runner and fixed DNS (set reliable DNS servers). Verified connectivity with:

- `nslookup actions.githubusercontent.com`
- `curl -I https://actions.githubusercontent.com`  
  **Evidence:** The `deploy` job successfully downloaded and displayed the `test-results` artifact.

### 3) App not starting under PM2 (missing dependencies)

**Symptom:** PM2 process stopped immediately; logs showed missing modules.  
**Root cause:** Dependencies were not installed on the self-hosted runner during the `deploy` job.  
**Solution:** Added dependency installation in the `deploy` job (Node 18 + `npm ci`/`npm install`) before PM2:

- `actions/setup-node@v4`
- `npm ci --omit=dev` (or `npm install`)  
  **Evidence:** PM2 shows `node-app` online and the workflow completes without errors.

### Deployment Process

1. **Cleanup**: Removes existing process if running

   ```bash
   pm2 delete node-app || true
   ```

2. **Start Application**: Launches with absolute path

   ```bash
   pm2 start "./src/server.js" --name node-app
   ```

3. **Save Process List**: Persists PM2 configuration
   ```bash
   pm2 save
   ```

### About The Applications

1. **Route**: This Application has 2 route

   ```bash
   / # this will show a hello world page
   ```

   ```bash
   /api # this will response a json
   ```

2. **Default Port**: By Default this application will run on port 3000
