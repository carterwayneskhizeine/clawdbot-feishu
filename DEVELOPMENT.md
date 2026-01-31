# Local Development Setup

This document explains how to link your local plugin development directory to OpenClaw for live testing and development.

## Prerequisites

- Node.js 22+ (preferably managed via [Volta](https://volta.sh))
- OpenClaw installed globally: `npm install -g openclaw`
- This plugin project cloned locally

## Method 1: Using `--link` (Recommended)

The `--link` flag creates a symbolic link to your local development directory, allowing code changes to take effect immediately without reinstallation.

### Step 1: Install Project Dependencies

```bash
cd /path/to/clawdbot-feishu
npm install
```

This installs OpenClaw as a development dependency, which is required for the plugin installation command.

### Step 2: Install Plugin with Link

```bash
openclaw plugins install -l .
```

**What this does:**
- Creates a symbolic link from `~/.openclaw/extensions/` to your local directory
- Adds the path to `plugins.load.paths` in your OpenClaw config
- Registers the plugin metadata

### Step 3: Verify Installation

```bash
openclaw plugins list
```

You should see:
```
│ Feishu       │ feishu   │ loaded   │ /path/to/clawdbot-feishu/index.ts │ 0.1.3 │
```

### Step 4: Restart Gateway

```bash
openclaw gateway restart
```

Your local plugin is now loaded! Any changes to the code will be reflected when the Gateway reloads the plugin.

## Method 2: Manual Path Configuration

Alternatively, manually add your plugin path to the OpenClaw configuration.

### Step 1: Install Dependencies

```bash
npm install
```

### Step 2: Add Path to Config

```bash
openclaw config set plugins.load.paths[0] "D:\\Code\\clawdbot-feishu" --type=json
```

### Step 3: Enable Plugin

```bash
openclaw plugins enable feishu
```

### Step 4: Restart Gateway

```bash
openclaw gateway restart
```

## Development Workflow

### Making Code Changes

1. Edit TypeScript source files in your local directory
2. OpenClaw loads plugins directly from `.ts` files (no build step required)
3. Restart the Gateway to reload changes:

```bash
openclaw gateway restart
```

### Type Checking

Run type checks without building:

```bash
npx tsc --noEmit
```

### Viewing Logs

Monitor real-time logs to see your plugin's output:

```bash
openclaw logs --follow
```

### Debugging

Get detailed plugin information:

```bash
# List all plugins with status
openclaw plugins list

# View plugin details
openclaw plugins info feishu

# Run diagnostics
openclaw plugins doctor
```

## Plugin Configuration

After installing the plugin, configure your Feishu app credentials:

```bash
openclaw config set channels.feishu.appId "cli_a9f6d8b1adb85cca"
openclaw config set channels.feishu.appSecret "your_app_secret"
openclaw config set channels.feishu.enabled true
```

### Other Useful Config

```bash
# Set DM policy (pairing, open, allowlist)
openclaw config set channels.feishu.dmPolicy "pairing"

# Set group policy
openclaw config set channels.feishu.groupPolicy "allowlist"

# Set render mode (auto, raw, card)
openclaw config set channels.feishu.renderMode "auto"
```

## Testing Your Changes

### Quick Test Cycle

1. Make code changes
2. Restart Gateway: `openclaw gateway restart`
3. Send a test message in Feishu
4. Check logs: `openclaw logs --follow`

### Hot Reloading

OpenClaw automatically reloads plugins when:
- The Gateway process restarts
- Configuration changes are detected

For most code changes, a Gateway restart is sufficient. No need to reinstall the plugin.

## Troubleshooting

### Plugin Not Found

**Error:** `plugin not found: feishu`

**Solution:** The plugin hasn't been installed yet. Run:
```bash
openclaw plugins install -l .
```

### Config Validation Errors

**Error:** `Invalid config ... plugin not found`

**Solution:** Install the plugin first, then configure it:
```bash
openclaw plugins install -l .
openclaw config set channels.feishu.enabled true
```

### Changes Not Taking Effect

**Problem:** Code changes don't appear to work.

**Solutions:**
1. Restart the Gateway: `openclaw gateway restart`
2. Check for TypeScript errors: `npx tsc --noEmit`
3. Verify the plugin is loaded: `openclaw plugins list`
4. Check logs for errors: `openclaw logs --follow`

### Permission Issues (Windows)

**Problem:** Need administrator privileges to run `openclaw` commands.

**Solution:** Run PowerShell as Administrator, or use the Windows Terminal with elevated permissions.

### Gateway Won't Start

**Problem:** Gateway fails to start after plugin changes.

**Solutions:**
1. Check syntax: `npx tsc --noEmit`
2. Review logs: `openclaw logs --follow`
3. Disable the plugin temporarily:
   ```bash
   openclaw plugins disable feishu
   ```
4. Fix the error, then re-enable:
   ```bash
   openclaw plugins enable feishu
   ```

## Uninstalling Local Plugin

To remove the symlink and use a published version instead:

```bash
# Uninstall the linked version
openclaw plugins uninstall feishu

# Install from npm (if published)
npm install -g @m1heng-clawd/feishu

# Or install from a local tarball
openclaw plugins install ./clawdbot-feishu-0.1.3.tgz
```

## Project Structure

```
clawdbot-feishu/
├── index.ts              # Plugin entry point
├── src/                  # Source code
│   ├── client.ts         # Feishu SDK client
│   ├── monitor.ts        # WebSocket event listener
│   ├── bot.ts            # Message handler
│   ├── send.ts           # Message sending
│   └── ...
├── openclaw.plugin.json  # Plugin manifest
├── package.json          # Dependencies and metadata
└── README.md             # User documentation
```

## Tips

- **No Build Required:** OpenClaw loads TypeScript files directly via `tsx`
- **Type Safety:** Always run `npx tsc --noEmit` before committing
- **Log Often:** Use `console.log` or the logging utilities in `src/` for debugging
- **Test Incrementally:** Test small changes frequently rather than large batches
- **Check Docs:** Refer to [OpenClaw Plugin Docs](https://docs.openclaw.ai/plugin) for advanced features

## Additional Resources

- [OpenClaw Documentation](https://docs.openclaw.ai/)
- [Plugin Manifest Reference](https://docs.openclaw.ai/plugins/manifest)
- [Feishu/Lark Open Platform](https://open.feishu.cn/)
- [Project README](./README.md)
