---
title: "🐛 [Bug]: Vite server does not allow connections from remote hosts"
labels: ["bug", "needs-triage"]
---

### Archon Version
Latest from `main` branch.

### Bug Severity
🟡 Medium - Affects functionality

### Bug Description
When deploying Archon to a remote server and accessing the UI from a custom domain (not `localhost`), the Vite development server blocks the connection. This prevents the UI from loading when following the remote deployment instructions.

### Steps to Reproduce
1. Deploy Archon to a remote server using `docker-compose`.
2. Set the `HOST` environment variable to the public domain of the server (e.g., `HOST=archon.example.com`) as per the `README.md` instructions.
3. Access the UI from a browser at `http://<your-host>:<your-port>`.
4. The browser shows an error and the connection is refused by Vite.

### Expected Behavior
The UI should load correctly when accessed from a remote host that has been specified in the `HOST` environment variable.

### Actual Behavior
The browser displays a "This host is not allowed" error from Vite. The full error is: `This host ("<your-host>") is not allowed. To allow this host, add "<your-host>" to server.allowedHosts in vite.config.js.`

### Error Details (if any)
```text
This host ("archon.80.225.216.153.sslip.io") is not allowed.
To allow this host, add "archon.80.225.216.153.sslip.io" to `server.allowedHosts` in vite.config.js.
```

### Affected Component
🖥️ User Interface

### Browser & OS
Any browser when accessing the UI remotely. The user was on Linux.

### Additional Context
This is a security feature of Vite's development server. The `README.md` correctly instructs users to set the `HOST` environment variable, but it does not mention the required change in `vite.config.ts` to allow the host. This will affect anyone trying to deploy Archon on a remote server and access it via a domain name or IP address.

**Proposed Solution:**
Add `allowedHosts: [externalHost]` to the `server` configuration in `archon-ui-main/vite.config.ts`. The `externalHost` variable is already defined in the file and correctly reads the `HOST` environment variable.

```typescript
// archon-ui-main/vite.config.ts
// ...
    server: {
      host: '0.0.0.0',
      port: 5173,
      strictPort: true,
      allowedHosts: [externalHost], // This line needs to be added
      proxy: {
// ...
```

### Service Status (check all that are working)
- [ ] 🖥️ Frontend UI (http://localhost:3737) - (Not accessible remotely due to this bug)
- [x] ⚙️ Main Server (http://localhost:8181)
- [x] 🔗 MCP Service (localhost:8051)
- [x] 🤖 Agents Service (http://localhost:8052)
- [x] 💾 Supabase Database (connected)
