# Xiaomi MiMo Orbit Token Plan Agent Setup Guide

This guide shows how to use a Xiaomi MiMo Token Plan key with beginner-friendly setup steps for compatible coding agents and automation tools. Most steps use the terminal because it is faster to copy, paste, and repeat.

Status checked: 2026-06-01.

Important model note: Xiaomi says the legacy `mimo-v2-pro` and `mimo-v2-omni` names start auto-routing to V2.5 on 2026-06-01 00:00 GMT+8 and are fully deprecated by 2026-06-30. Use `mimo-v2.5-pro` unless you have a specific reason not to.

## Supported Tools

Officially documented compatible tools:

| Tool No. | Tool          | Connection Type        | Terminal Setup? |
| -------- | ------------- | ---------------------- | --------------- |
| 1        | OpenCode      | OpenAI-compatible      | Yes             |
| 2        | OpenClaw      | OpenAI-compatible      | Yes             |
| 3        | Claude Code   | Anthropic-compatible   | Yes             |
| 4        | Hermes Agent  | OpenAI-compatible      | Yes             |
| 5        | Kilo Code     | OpenAI-compatible      | Yes             |
| 6        | Cline         | OpenAI-compatible      | Yes             |
| 7        | Cherry Studio | OpenAI-compatible      | No              |
| 8        | Qwen Code     | OpenAI-compatible      | Yes             |
| 9        | CodeBuddy     | OpenAI-compatible      | Yes             |
| 10       | n8n           | OpenAI-compatible HTTP | Mostly          |

## 1. Get Token Plan Credentials

From the Xiaomi MiMo Open Platform subscription page, copy:

- `MIMO_API_KEY`: Token Plan key, starts with `tp-`.
- `MIMO_BASE_URL_OPENAI`: OpenAI-compatible Token Plan base URL.
- `MIMO_BASE_URL_ANTHROPIC`: Anthropic-compatible Token Plan base URL.

Do not mix key types:

- Token Plan key: `tp-xxxxx`
- Pay-as-you-go key: `sk-xxxxx`

Token Plan base URLs:

| Region    | OpenAI-compatible base URL                 | Anthropic-compatible base URL                     |
| --------- | ------------------------------------------ | ------------------------------------------------- |
| China     | `https://token-plan-cn.xiaomimimo.com/v1`  | `https://token-plan-cn.xiaomimimo.com/anthropic`  |
| Singapore | `https://token-plan-sgp.xiaomimimo.com/v1` | `https://token-plan-sgp.xiaomimimo.com/anthropic` |
| Europe    | `https://token-plan-ams.xiaomimimo.com/v1` | `https://token-plan-ams.xiaomimimo.com/anthropic` |

Pick the exact region shown in your Xiaomi subscription console.

## 2. Set Environment Variables

### PowerShell

Temporary variables for the current terminal:

```powershell
$env:MIMO_API_KEY = "tp-your-token-plan-key"
$env:MIMO_BASE_URL_OPENAI = "https://token-plan-sgp.xiaomimimo.com/v1"
$env:MIMO_BASE_URL_ANTHROPIC = "https://token-plan-sgp.xiaomimimo.com/anthropic"
$env:MIMO_MODEL = "mimo-v2.5-pro"
```

Optional persistent user variables:

```powershell
[Environment]::SetEnvironmentVariable("MIMO_API_KEY", "tp-your-token-plan-key", "User")
[Environment]::SetEnvironmentVariable("MIMO_BASE_URL_OPENAI", "https://token-plan-sgp.xiaomimimo.com/v1", "User")
[Environment]::SetEnvironmentVariable("MIMO_BASE_URL_ANTHROPIC", "https://token-plan-sgp.xiaomimimo.com/anthropic", "User")
[Environment]::SetEnvironmentVariable("MIMO_MODEL", "mimo-v2.5-pro", "User")
```

Open a new terminal after setting persistent variables.

### Bash, Zsh, Git Bash, WSL

```bash
export MIMO_API_KEY="tp-your-token-plan-key"
export MIMO_BASE_URL_OPENAI="https://token-plan-sgp.xiaomimimo.com/v1"
export MIMO_BASE_URL_ANTHROPIC="https://token-plan-sgp.xiaomimimo.com/anthropic"
export MIMO_MODEL="mimo-v2.5-pro"
```

To persist in Bash:

```bash
cat >> ~/.bashrc <<'EOF'
export MIMO_API_KEY="tp-your-token-plan-key"
export MIMO_BASE_URL_OPENAI="https://token-plan-sgp.xiaomimimo.com/v1"
export MIMO_BASE_URL_ANTHROPIC="https://token-plan-sgp.xiaomimimo.com/anthropic"
export MIMO_MODEL="mimo-v2.5-pro"
EOF
source ~/.bashrc
```

## 3. Verify MiMo API Access Directly

PowerShell:

```powershell
$body = @{
  model = $env:MIMO_MODEL
  messages = @(
    @{ role = "user"; content = "Reply with OK and the model name." }
  )
  max_completion_tokens = 128
} | ConvertTo-Json -Depth 10

curl.exe -sS "$($env:MIMO_BASE_URL_OPENAI)/chat/completions" `
  -H "api-key: $env:MIMO_API_KEY" `
  -H "Content-Type: application/json" `
  -d $body
```

Bash:

```bash
curl -sS "$MIMO_BASE_URL_OPENAI/chat/completions" \
  -H "api-key: $MIMO_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "mimo-v2.5-pro",
    "messages": [
      { "role": "user", "content": "Reply with OK and the model name." }
    ],
    "max_completion_tokens": 128
  }'
```

If this fails, fix credentials before configuring agents.

## 4. Prerequisites

Check your runtime:

```powershell
node -v
npm.cmd -v
git --version
```

PowerShell may block `npm.ps1` with an execution policy error. Use `npm.cmd` in PowerShell:

```powershell
npm.cmd install -g <package-name>
```

If you prefer Git Bash:

```powershell
winget install --id Git.Git -e
```

OpenClaw requires Node.js 22 or newer. Most other CLIs require Node.js 18 or newer, though Cline recommends Node.js 22.

## 5. OpenCode

Install:

```powershell
npm.cmd install -g opencode-ai
opencode.cmd --version
```

Install on Bash, Git Bash, or WSL:

```bash
npm install -g opencode-ai
opencode --version
```

Create config on PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.config\opencode" | Out-Null
@'
{
  "$schema": "https://opencode.ai/config.json",
  "model": "mimo/mimo-v2.5-pro",
  "small_model": "mimo/mimo-v2.5-pro",
  "provider": {
    "mimo": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "MiMo",
      "options": {
        "baseURL": "{env:MIMO_BASE_URL_OPENAI}",
        "apiKey": "{env:MIMO_API_KEY}"
      },
      "models": {
        "mimo-v2.5-pro": {
          "name": "mimo-v2.5-pro",
          "limit": {
            "context": 1048576,
            "output": 131072
          },
          "modalities": {
            "input": ["text"],
            "output": ["text"]
          }
        },
        "mimo-v2.5": {
          "name": "mimo-v2.5",
          "limit": {
            "context": 1048576,
            "output": 131072
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        }
      }
    }
  }
}
'@ | Set-Content "$HOME\.config\opencode\opencode.json" -Encoding UTF8
```

Create config on Bash, Git Bash, or WSL:

```bash
mkdir -p ~/.config/opencode
cat > ~/.config/opencode/opencode.json <<'EOF'
{
  "$schema": "https://opencode.ai/config.json",
  "model": "mimo/mimo-v2.5-pro",
  "small_model": "mimo/mimo-v2.5-pro",
  "provider": {
    "mimo": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "MiMo",
      "options": {
        "baseURL": "{env:MIMO_BASE_URL_OPENAI}",
        "apiKey": "{env:MIMO_API_KEY}"
      },
      "models": {
        "mimo-v2.5-pro": {
          "name": "mimo-v2.5-pro",
          "limit": {
            "context": 1048576,
            "output": 131072
          },
          "modalities": {
            "input": ["text"],
            "output": ["text"]
          }
        },
        "mimo-v2.5": {
          "name": "mimo-v2.5",
          "limit": {
            "context": 1048576,
            "output": 131072
          },
          "modalities": {
            "input": ["text", "image"],
            "output": ["text"]
          }
        }
      }
    }
  }
}
EOF
```

Run:

```powershell
opencode.cmd
```

Run on Bash, Git Bash, or WSL:

```bash
opencode
```

Inside OpenCode:

```text
/models
/status
```

## 6. OpenClaw

OpenClaw is a general agent. Token Plan requires manual config instead of only the onboarding preset.

Install on Windows PowerShell:

```powershell
iwr -useb https://openclaw.ai/install.ps1 | iex
```

Install on macOS/Linux:

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

Start onboarding if needed:

```powershell
openclaw onboard --install-daemon
```

Start onboarding on Bash, Git Bash, or WSL:

```bash
openclaw onboard --install-daemon
```

For Token Plan, edit `~/.openclaw/openclaw.json`. Back up your current file first:

```powershell
Copy-Item "$HOME\.openclaw\openclaw.json" "$HOME\.openclaw\openclaw.backup.json" -ErrorAction SilentlyContinue
```

Back up on Bash, Git Bash, or WSL:

```bash
mkdir -p ~/.openclaw
cp ~/.openclaw/openclaw.json ~/.openclaw/openclaw.backup.json 2>/dev/null || true
```

Merge this provider configuration into the file. Do not name the custom provider `xiaomi`; use a separate name such as `xiaomi-coding`.

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "xiaomi-coding": {
        "baseUrl": "BASE_URL",
        "apiKey": "API_KEY",
        "api": "openai-completions",
        "models": [
          {
            "id": "mimo-v2.5-pro",
            "name": "mimo-v2.5-pro",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 1048576,
            "maxTokens": 32000
          },
          {
            "id": "mimo-v2.5",
            "name": "mimo-v2.5",
            "reasoning": true,
            "input": ["text", "image"],
            "contextWindow": 262144,
            "maxTokens": 32000
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "xiaomi-coding/mimo-v2.5-pro"
      },
      "models": {
        "xiaomi-coding/mimo-v2.5": {},
        "xiaomi-coding/mimo-v2.5-pro": {}
      }
    }
  }
}
```

Replace:

- `BASE_URL` with your OpenAI-compatible Token Plan base URL.
- `API_KEY` with your `tp-` Token Plan key.

Run:

```powershell
openclaw tui
```

Run on Bash, Git Bash, or WSL:

```bash
openclaw tui
```

Or open the Web UI link printed by OpenClaw.

### OpenClaw on a VPS with MiMo env variables

If OpenClaw is deployed with Docker Compose on a VPS, put the MiMo key and base URL in the OpenClaw `.env` file instead of hard-coding secrets in multiple places.

Example `.env`:

```env
PORT=61125
TZ=Asia/Manila
OPENCLAW_HOST=openclaw.example.com
OPENCLAW_GATEWAY_TOKEN=replace-with-a-strong-token
MIMO_API_KEY=tp-replace-with-your-token-plan-key
MIMO_BASE_URL_OPENAI=https://token-plan-sgp.xiaomimimo.com/v1
MIMO_MODEL=mimo-v2.5-pro
```

If the container already uses `OPENAI_API_KEY`, keep it only if OpenClaw needs it for another purpose. For MiMo Token Plan setup, the important values are `MIMO_API_KEY` and `MIMO_BASE_URL_OPENAI`.

Use the correct Token Plan region for `MIMO_BASE_URL_OPENAI`:

```text
https://token-plan-cn.xiaomimimo.com/v1
https://token-plan-sgp.xiaomimimo.com/v1
https://token-plan-ams.xiaomimimo.com/v1
```

Make sure the Compose service loads that file:

```yaml
env_file:
  - .env
```

Then create or update OpenClaw's config inside the OpenClaw container. In Hostinger Docker Manager, use the `Terminal` button for the OpenClaw project.

```bash
mkdir -p "$HOME/.openclaw"
cp "$HOME/.openclaw/openclaw.json" "$HOME/.openclaw/openclaw.backup.json" 2>/dev/null || true

cat > "$HOME/.openclaw/openclaw.json" <<EOF
{
  "models": {
    "mode": "merge",
    "providers": {
      "xiaomi-coding": {
        "baseUrl": "${MIMO_BASE_URL_OPENAI}",
        "apiKey": "${MIMO_API_KEY}",
        "api": "openai-completions",
        "models": [
          {
            "id": "mimo-v2.5-pro",
            "name": "mimo-v2.5-pro",
            "reasoning": true,
            "input": ["text"],
            "contextWindow": 1048576,
            "maxTokens": 32000
          },
          {
            "id": "mimo-v2.5",
            "name": "mimo-v2.5",
            "reasoning": true,
            "input": ["text", "image"],
            "contextWindow": 262144,
            "maxTokens": 32000
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "xiaomi-coding/mimo-v2.5-pro"
      },
      "models": {
        "xiaomi-coding/mimo-v2.5-pro": {},
        "xiaomi-coding/mimo-v2.5": {}
      }
    }
  }
}
EOF
```

Restart the container:

```bash
docker compose restart openclaw
```

After restarting, refresh the OpenClaw web page and choose `mimo-v2.5-pro` or `xiaomi-coding/mimo-v2.5-pro` from the model dropdown.

## 7. Claude Code

Claude Code uses the Anthropic-compatible MiMo endpoint.

Install:

```powershell
npm.cmd install -g @anthropic-ai/claude-code
claude --version
```

Install on Bash, Git Bash, or WSL:

```bash
npm install -g @anthropic-ai/claude-code
claude --version
```

Clear conflicting Anthropic variables in the current terminal:

```powershell
Remove-Item Env:ANTHROPIC_AUTH_TOKEN -ErrorAction SilentlyContinue
Remove-Item Env:ANTHROPIC_BASE_URL -ErrorAction SilentlyContinue
```

Clear conflicting Anthropic variables in Bash, Git Bash, or WSL:

```bash
unset ANTHROPIC_AUTH_TOKEN
unset ANTHROPIC_BASE_URL
```

Create Claude Code settings on PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude" | Out-Null

$settings = @{
  env = @{
    ANTHROPIC_BASE_URL = $env:MIMO_BASE_URL_ANTHROPIC
    ANTHROPIC_AUTH_TOKEN = $env:MIMO_API_KEY
    ANTHROPIC_MODEL = $env:MIMO_MODEL
    ANTHROPIC_DEFAULT_SONNET_MODEL = $env:MIMO_MODEL
    ANTHROPIC_DEFAULT_OPUS_MODEL = $env:MIMO_MODEL
    ANTHROPIC_DEFAULT_HAIKU_MODEL = $env:MIMO_MODEL
  }
}

$settings | ConvertTo-Json -Depth 10 | Set-Content "$HOME\.claude\settings.json" -Encoding UTF8
@{ hasCompletedOnboarding = $true } | ConvertTo-Json | Set-Content "$HOME\.claude.json" -Encoding UTF8
```

Create Claude Code settings on Bash, Git Bash, or WSL:

```bash
mkdir -p ~/.claude
cat > ~/.claude/settings.json <<EOF
{
  "env": {
    "ANTHROPIC_BASE_URL": "$MIMO_BASE_URL_ANTHROPIC",
    "ANTHROPIC_AUTH_TOKEN": "$MIMO_API_KEY",
    "ANTHROPIC_MODEL": "$MIMO_MODEL",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "$MIMO_MODEL",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "$MIMO_MODEL",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "$MIMO_MODEL"
  }
}
EOF

cat > ~/.claude.json <<'EOF'
{
  "hasCompletedOnboarding": true
}
EOF
```

Run:

```powershell
claude
```

Inside Claude Code:

```text
/status
/context
```

For 1M context, Xiaomi documents appending `[1m]` to supported model IDs, for example `mimo-v2.5-pro[1m]`.

### Switch Claude Code back to normal Anthropic

Use this when Claude Code was pointed to MiMo and should go back to the official Anthropic Claude Code login and models.

PowerShell:

```powershell
# Back up the MiMo-specific settings first.
Copy-Item "$HOME\.claude\settings.json" "$HOME\.claude\settings.mimo.backup.json" -ErrorAction SilentlyContinue

# Remove the MiMo override file.
Remove-Item "$HOME\.claude\settings.json" -ErrorAction SilentlyContinue

# Clear MiMo/Anthropic-compatible overrides from the current terminal.
Remove-Item Env:ANTHROPIC_BASE_URL -ErrorAction SilentlyContinue
Remove-Item Env:ANTHROPIC_AUTH_TOKEN -ErrorAction SilentlyContinue
Remove-Item Env:ANTHROPIC_MODEL -ErrorAction SilentlyContinue
Remove-Item Env:ANTHROPIC_DEFAULT_SONNET_MODEL -ErrorAction SilentlyContinue
Remove-Item Env:ANTHROPIC_DEFAULT_OPUS_MODEL -ErrorAction SilentlyContinue
Remove-Item Env:ANTHROPIC_DEFAULT_HAIKU_MODEL -ErrorAction SilentlyContinue

# Start Claude Code normally and log in when prompted.
claude
```

If PowerShell blocks the npm `.ps1` launcher, use:

```powershell
claude.cmd
```

Bash, Git Bash, or WSL:

```bash
# Back up the MiMo-specific settings first.
cp ~/.claude/settings.json ~/.claude/settings.mimo.backup.json 2>/dev/null || true

# Remove the MiMo override file.
rm -f ~/.claude/settings.json

# Clear MiMo/Anthropic-compatible overrides from the current terminal.
unset ANTHROPIC_BASE_URL
unset ANTHROPIC_AUTH_TOKEN
unset ANTHROPIC_MODEL
unset ANTHROPIC_DEFAULT_SONNET_MODEL
unset ANTHROPIC_DEFAULT_OPUS_MODEL
unset ANTHROPIC_DEFAULT_HAIKU_MODEL

# Start Claude Code normally and log in when prompted.
claude
```

If those variables were added to `.bashrc`, `.zshrc`, PowerShell profile, or a system environment-variable manager, remove them there too. Otherwise, they may come back when a new terminal opens.

## 8. Hermes Agent

Hermes Agent is a general agent. Xiaomi MiMo is already listed as a predefined provider, so most users should start with Quick Setup. On Windows, use WSL2.

Install in Linux/macOS/WSL2:

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
source ~/.bashrc
hermes --version
```

### Method 1: Quick Setup with Xiaomi MiMo

Run the setup wizard:

```bash
hermes setup
```

Choose:

1. `Quick setup - provider, model & messaging`
2. Provider: `Xiaomi MiMo`
3. Enter the Xiaomi API key when prompted.
4. Confirm or replace the Base URL when prompted.
5. Select the default model, for example `mimo-v2.5-pro`.

If setup was skipped the first time, run `hermes setup` again.

For Token Plan users, the predefined Xiaomi MiMo provider can still be used. When prompted, use the Token Plan key and the Token Plan OpenAI-compatible Base URL:

```text
https://token-plan-cn.xiaomimimo.com/v1
https://token-plan-sgp.xiaomimimo.com/v1
https://token-plan-ams.xiaomimimo.com/v1
```

If Hermes was already configured for pay-as-you-go and should switch to Token Plan, edit `~/.hermes/.env` and replace:

```env
XIAOMI_API_KEY=tp-your-token-plan-key
XIAOMI_BASE_URL=https://token-plan-sgp.xiaomimimo.com/v1
```

Then open a new terminal or reload the shell:

```bash
source ~/.bashrc
hermes doctor
```

### Method 2: Custom OpenAI-Compatible Provider

Use this method if Quick Setup does not fit the credential type, if the predefined provider is not available, or if a guide specifically asks for custom provider configuration.

```bash
hermes config set model.provider custom
hermes config set model.base_url "$MIMO_BASE_URL_OPENAI"
hermes config set model.api_key "$MIMO_API_KEY"
hermes config set model.default "$MIMO_MODEL"
```

For Token Plan, the values should look like this:

```bash
export MIMO_API_KEY="tp-your-token-plan-key"
export MIMO_BASE_URL_OPENAI="https://token-plan-sgp.xiaomimimo.com/v1"
export MIMO_MODEL="mimo-v2.5-pro"
```

Verify:

```bash
hermes doctor
```

Run:

```bash
hermes
hermes --tui
```

## 9. Kilo Code

### If Kilo Is Not Recognized

On Windows, Kilo may work in PowerShell but not in Git Bash or the VS Code terminal. This usually means the terminal cannot find the folder where `kilo.exe` was installed.

First, check whether PowerShell can find Kilo:

```text
where kilo
C:\Users\<YOUR_WINDOWS_USER>\.kilo\bin\kilo.exe

kilo --version
<version number>
```

If plain `bash` opens the old Windows/WSL launcher instead of Git Bash, it may point to:

```text
C:\Windows\System32\bash.exe
```

That launcher is not the same as Git Bash. If no WSL distro is installed, it can fail with "The system cannot find the file specified." Use PowerShell or launch Git Bash directly.

PowerShell usage:

```powershell
kilo --version
kilo --help
kilo auth list
kilo
```

Launch Git Bash directly:

```powershell
& "C:\Program Files (x86)\Git\bin\bash.exe" -lc "kilo --version"
& "C:\Program Files (x86)\Git\git-bash.exe"
```

Inside Git Bash, if `kilo` is not found:

```bash
echo 'export PATH="$PATH:$HOME/.kilo/bin"' >> ~/.bashrc
source ~/.bashrc
kilo --version
```

If VS Code still opens a Git Bash terminal that cannot find `kilo`, make sure login shells load `.bashrc`:

```bash
cat > ~/.bash_profile <<'EOF'
# Load interactive Git Bash config.
if [ -f ~/.bashrc ]; then
  . ~/.bashrc
fi
EOF

source ~/.bash_profile
kilo --version
```

If you are inside WSL, install Kilo separately inside WSL:

```bash
npm install -g @kilocode/cli
kilo --version
```

Note: Xiaomi's Kilo page may show `kilocode`, but many current Kilo CLI installs use the command `kilo`.

### Install And Configure

Install or update:

```powershell
npm.cmd install -g @kilocode/cli
kilo --version
```

Install or update on Bash, Git Bash, or WSL:

```bash
npm install -g @kilocode/cli
kilo --version
```

Create config on PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.config\kilo" | Out-Null
@'
{
  "$schema": "https://kilo.ai/config.json",
  "model": "mimo/mimo-v2.5-pro",
  "disabled_providers": [],
  "provider": {
    "mimo": {
      "name": "MiMo",
      "npm": "@ai-sdk/openai-compatible",
      "models": {
        "mimo-v2.5-pro": {
          "name": "mimo-v2.5-pro",
          "options": {
            "thinking": {
              "type": "enabled"
            }
          }
        }
      },
      "options": {
        "apiKey": "{env:MIMO_API_KEY}",
        "baseURL": "{env:MIMO_BASE_URL_OPENAI}"
      }
    }
  },
  "permission": {
    "bash": "ask",
    "edit": "ask"
  }
}
'@ | Set-Content "$HOME\.config\kilo\config.json" -Encoding UTF8

# Some current Kilo builds also read the OpenCode-style filename.
Copy-Item "$HOME\.config\kilo\config.json" "$HOME\.config\kilo\opencode.json" -Force
```

Create config on Bash, Git Bash, or WSL:

```bash
mkdir -p ~/.config/kilo
cat > ~/.config/kilo/config.json <<'EOF'
{
  "$schema": "https://kilo.ai/config.json",
  "model": "mimo/mimo-v2.5-pro",
  "disabled_providers": [],
  "provider": {
    "mimo": {
      "name": "MiMo",
      "npm": "@ai-sdk/openai-compatible",
      "models": {
        "mimo-v2.5-pro": {
          "name": "mimo-v2.5-pro",
          "options": {
            "thinking": {
              "type": "enabled"
            }
          }
        }
      },
      "options": {
        "apiKey": "{env:MIMO_API_KEY}",
        "baseURL": "{env:MIMO_BASE_URL_OPENAI}"
      }
    }
  },
  "permission": {
    "bash": "ask",
    "edit": "ask"
  }
}
EOF

cp ~/.config/kilo/config.json ~/.config/kilo/opencode.json
```

Run:

```powershell
kilo config check
kilo models mimo
kilo
```

Run on Bash, Git Bash, or WSL:

```bash
kilo config check
kilo models mimo
kilo
```

Non-interactive run:

```powershell
kilo run -m mimo/mimo-v2.5-pro "Explain the project structure and list next setup steps."
```

Non-interactive run on Bash, Git Bash, or WSL:

```bash
kilo run -m mimo/mimo-v2.5-pro "Explain the project structure and list next setup steps."
```

Inside Kilo TUI:

```text
/connect
/models
/agents
/status
/exit
```

## 10. Cline CLI

Install:

```powershell
npm.cmd install -g cline
cline.cmd --version
```

Install on Bash, Git Bash, or WSL:

```bash
npm install -g cline
cline --version
```

Configure:

```powershell
cline.cmd auth -p openai -k "$env:MIMO_API_KEY" -b "$env:MIMO_BASE_URL_OPENAI" -m "$env:MIMO_MODEL"
```

Configure on Bash, Git Bash, or WSL:

```bash
cline auth -p openai -k "$MIMO_API_KEY" -b "$MIMO_BASE_URL_OPENAI" -m "$MIMO_MODEL"
```

Run:

```powershell
cline.cmd
```

Run on Bash, Git Bash, or WSL:

```bash
cline
```

For the classic terminal interface:

```powershell
cline.cmd --tui
```

For the classic terminal interface on Bash, Git Bash, or WSL:

```bash
cline --tui
```

## 11. Cherry Studio

Cherry Studio is a desktop app, so most setup happens inside the app instead of the terminal.

Setup:

1. Install Cherry Studio from `https://www.cherry-ai.com` or `https://github.com/CherryHQ/cherry-studio`.
2. Open settings.
3. Go to Model Services.
4. Search for `Xiaomi MiMo`.
5. For Token Plan, replace both the API key and API host with the Token Plan values.
6. Select `mimo-v2.5-pro` from the model list.

Note: Xiaomi documents that Token Plan is temporarily unavailable in Cherry Studio Agent mode.

## 12. Qwen Code

Install on Windows:

```cmd
curl -fsSL -o %TEMP%\install-qwen.bat https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen.bat && %TEMP%\install-qwen.bat --source bailian
qwen --version
```

Install on macOS/Linux:

```bash
bash -c "$(curl -fsSL https://qwen-code-assets.oss-cn-hangzhou.aliyuncs.com/installation/install-qwen.sh)" -s --source bailian
qwen --version
```

Create settings on PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.qwen" | Out-Null

$qwenSettings = [ordered]@{
  env = @{
    MIMO_API_KEY = $env:MIMO_API_KEY
  }
  modelProviders = @{
    openai = @(
      @{
        id = $env:MIMO_MODEL
        name = $env:MIMO_MODEL
        baseUrl = $env:MIMO_BASE_URL_OPENAI
        envKey = "MIMO_API_KEY"
      }
    )
  }
  security = @{
    auth = @{
      selectedType = "openai"
    }
  }
  model = @{
    name = $env:MIMO_MODEL
  }
  '$version' = 3
}

$qwenSettings | ConvertTo-Json -Depth 10 | Set-Content "$HOME\.qwen\settings.json" -Encoding UTF8
```

Create settings on Bash, Git Bash, or WSL:

```bash
mkdir -p ~/.qwen
cat > ~/.qwen/settings.json <<EOF
{
  "env": {
    "MIMO_API_KEY": "$MIMO_API_KEY"
  },
  "modelProviders": {
    "openai": [
      {
        "id": "$MIMO_MODEL",
        "name": "$MIMO_MODEL",
        "baseUrl": "$MIMO_BASE_URL_OPENAI",
        "envKey": "MIMO_API_KEY"
      }
    ]
  },
  "security": {
    "auth": {
      "selectedType": "openai"
    }
  },
  "model": {
    "name": "$MIMO_MODEL"
  },
  "\$version": 3
}
EOF
```

Run:

```powershell
qwen
```

Run on Bash, Git Bash, or WSL:

```bash
qwen
```

## 13. CodeBuddy CLI

Install:

```powershell
npm.cmd install -g @tencent-ai/codebuddy-code
codebuddy.cmd --version
```

Install on Bash, Git Bash, or WSL:

```bash
npm install -g @tencent-ai/codebuddy-code
codebuddy --version
```

Create model config on PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.codebuddy" | Out-Null

$models = @{
  models = @(
    @{
      id = "mimo-v2.5-pro"
      name = "mimo-v2.5-pro"
      vendor = "MiMo"
      apiKey = $env:MIMO_API_KEY
      url = "$($env:MIMO_BASE_URL_OPENAI)/chat/completions"
      supportsToolCall = $true
      supportsImages = $false
    },
    @{
      id = "mimo-v2.5"
      name = "mimo-v2.5"
      vendor = "MiMo"
      apiKey = $env:MIMO_API_KEY
      url = "$($env:MIMO_BASE_URL_OPENAI)/chat/completions"
      supportsToolCall = $true
      supportsImages = $true
    }
  )
}

$models | ConvertTo-Json -Depth 10 | Set-Content "$HOME\.codebuddy\models.json" -Encoding UTF8
```

Create model config on Bash, Git Bash, or WSL:

```bash
mkdir -p ~/.codebuddy
cat > ~/.codebuddy/models.json <<EOF
{
  "models": [
    {
      "id": "mimo-v2.5-pro",
      "name": "mimo-v2.5-pro",
      "vendor": "MiMo",
      "apiKey": "$MIMO_API_KEY",
      "url": "${MIMO_BASE_URL_OPENAI}/chat/completions",
      "supportsToolCall": true,
      "supportsImages": false
    },
    {
      "id": "mimo-v2.5",
      "name": "mimo-v2.5",
      "vendor": "MiMo",
      "apiKey": "$MIMO_API_KEY",
      "url": "${MIMO_BASE_URL_OPENAI}/chat/completions",
      "supportsToolCall": true,
      "supportsImages": true
    }
  ]
}
EOF
```

Run:

```powershell
codebuddy.cmd
```

Run on Bash, Git Bash, or WSL:

```bash
codebuddy
```

If CodeBuddy asks for a login method, choose:

```text
Log in via International Site
```

Use the other choices only for these cases:

| Option | Use When |
| --- | --- |
| `Log in via Chinese Site` | The account is a mainland China Tencent/CodeBuddy account. |
| `Log in via Enterprise Domain` | A company provided a specific enterprise CodeBuddy domain. |
| `Log in via iOA` | Tencent internal account only. |

Inside CodeBuddy:

```text
/model
/status
```

After login, open the model selector:

```text
/model
```

Choose:

```text
mimo-v2.5-pro
```

## 14. n8n Setup

n8n is useful for automation workflows that call MiMo through an OpenAI-compatible HTTP endpoint.

### Run n8n with Docker

PowerShell:

```powershell
docker volume create n8n_data

docker run -it --rm `
  --name n8n `
  -p 5678:5678 `
  -e GENERIC_TIMEZONE="Asia/Manila" `
  -e TZ="Asia/Manila" `
  -e N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true `
  -e N8N_RUNNERS_ENABLED=true `
  -e MIMO_API_KEY="$env:MIMO_API_KEY" `
  -e MIMO_BASE_URL_OPENAI="$env:MIMO_BASE_URL_OPENAI" `
  -e MIMO_MODEL="$env:MIMO_MODEL" `
  -v n8n_data:/home/node/.n8n `
  docker.n8n.io/n8nio/n8n
```

Bash, Git Bash, or WSL:

```bash
docker volume create n8n_data

docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -e GENERIC_TIMEZONE="Asia/Manila" \
  -e TZ="Asia/Manila" \
  -e N8N_ENFORCE_SETTINGS_FILE_PERMISSIONS=true \
  -e N8N_RUNNERS_ENABLED=true \
  -e MIMO_API_KEY="$MIMO_API_KEY" \
  -e MIMO_BASE_URL_OPENAI="$MIMO_BASE_URL_OPENAI" \
  -e MIMO_MODEL="$MIMO_MODEL" \
  -v n8n_data:/home/node/.n8n \
  docker.n8n.io/n8nio/n8n
```

Open:

```text
http://localhost:5678
```

### Use MiMo in an HTTP Request Node

Create a workflow:

1. Add `Manual Trigger`.
2. Add `HTTP Request`.
3. Set method to `POST`.
4. Set URL to `https://token-plan-sgp.xiaomimimo.com/v1/chat/completions` or your actual Token Plan base URL plus `/chat/completions`.
5. Add headers:
   - `api-key`: your `tp-` Token Plan key.
   - `Content-Type`: `application/json`.
6. Set JSON body:

```json
{
  "model": "mimo-v2.5-pro",
  "messages": [
    {
      "role": "user",
      "content": "Write a one sentence summary of what n8n is."
    }
  ],
  "max_completion_tokens": 256
}
```

### Use MiMo in an n8n AI Agent

If your n8n version exposes a custom base URL in OpenAI credentials:

1. Add an `AI Agent` node.
2. Connect an `OpenAI Chat Model` or `LM OpenAI` sub-node.
3. Create OpenAI credentials using:
   - API Key: your `tp-` key.
   - Base URL: your Token Plan OpenAI-compatible base URL.
4. Set model to `mimo-v2.5-pro`.
5. Connect tools to the AI Agent node as needed.

If your n8n version does not expose a custom base URL, use the HTTP Request method above. It is the most explicit and reliable method for OpenAI-compatible endpoints.

## 15. Troubleshooting

### `npm` fails in PowerShell because scripts are disabled

Use `npm.cmd`:

```powershell
npm.cmd install -g opencode-ai
```

In Bash, Git Bash, or WSL, use normal `npm`:

```bash
npm install -g opencode-ai
```

### `kilo` works in PowerShell but not in `bash`

You are likely running the Windows/WSL `bash.exe`, not Git Bash. Launch Git Bash directly:

```powershell
& "C:\Program Files (x86)\Git\git-bash.exe"
```

Or use PowerShell:

```powershell
kilo
```

If Git Bash is already open, refresh the shell after changing `.bashrc`:

```bash
source ~/.bashrc
hash -r
kilo --version
```

### `kilocode` is not recognized

Try `kilo` instead:

```powershell
kilo --version
kilo
```

In Bash, Git Bash, or WSL:

```bash
kilo --version
kilo
```

Many current Kilo CLI installs use `kilo.exe` as the command.

### `opencode` is command not found in Git Bash

First check whether npm created the command files:

```powershell
where opencode
opencode.cmd --version
```

Check from Bash, Git Bash, or WSL:

```bash
command -v opencode
opencode --version
```

If `where opencode` finds nothing, reinstall the package:

```powershell
npm.cmd install -g opencode-ai@latest
```

Reinstall from Bash, Git Bash, or WSL:

```bash
npm install -g opencode-ai@latest
```

Then in the already-open Git Bash terminal, refresh Bash command lookup:

```bash
hash -r
command -v opencode
opencode --version
```

On Windows, the Git Bash path usually looks like this:

```text
/c/Program Files/nodejs/opencode
```

### `qwen` is command not found in Git Bash

First check whether npm created the command files:

```powershell
where qwen
qwen.cmd --version
```

Check from Bash, Git Bash, or WSL:

```bash
command -v qwen
qwen --version
```

If `where qwen` finds nothing, reinstall the package:

```powershell
npm.cmd install -g @qwen-code/qwen-code@latest
```

Reinstall from Bash, Git Bash, or WSL:

```bash
npm install -g @qwen-code/qwen-code@latest
```

Then in the already-open Git Bash terminal, refresh Bash command lookup:

```bash
hash -r
command -v qwen
qwen --version
```

On Windows, the Git Bash path usually looks like this:

```text
/c/Program Files/nodejs/qwen
```

### Wrong URL or API style errors

For OpenCode, Kilo, OpenClaw, Cline, Qwen Code, CodeBuddy, and n8n, prefer the OpenAI-compatible URL:

```text
https://token-plan-<region>.xiaomimimo.com/v1
```

Use the Anthropic-compatible URL mainly for Claude Code:

```text
https://token-plan-<region>.xiaomimimo.com/anthropic
```

### Claude Code still uses MiMo after switching back

Claude Code may still use MiMo if override variables are saved in `~/.claude/settings.json` or in the terminal profile. Use the `Switch Claude Code back to normal Anthropic` steps in the Claude Code section, then open a new terminal and run `claude` again.

### OpenClaw exits with code 78 while enabling Telegram

If OpenClaw gets stuck on the startup screen and the logs show something like this:

```text
Enabling "telegram" plugin...
Appending plugin "telegram" configuration
Starting OpenClaw gateway...
OpenClaw exited with code 78
OpenClaw process exited before device approval could complete
```

Temporarily remove `TELEGRAM_BOT_TOKEN` from the OpenClaw environment and redeploy. First confirm OpenClaw starts correctly with MiMo. After that, add Telegram back later with a fresh BotFather token and no extra spaces or newlines.

### Hostinger VPS: n8n, Hermes Agent, and OpenClaw on the same server

If n8n, Hermes Agent, and OpenClaw run on the same Hostinger VPS, use one shared Traefik reverse proxy for HTTPS. Only Traefik should publish ports `80` and `443`.

Common symptom:

```text
Secure browser context required
This page is running over plain HTTP
```

This often happens when OpenClaw is opened directly with an IP and port, for example:

```text
http://SERVER_IP:61125
ws://SERVER_IP:61125
```

OpenClaw needs a secure browser context. Use HTTPS and WSS through Traefik instead:

```text
https://openclaw.example.com
wss://openclaw.example.com
```

Use this checklist:

1. Create one shared Docker network:

```bash
docker network create proxy
```

If the network already exists, Docker will show an error. That is fine.

2. Run only one Traefik container on the VPS.

3. Put n8n, Hermes Agent, and OpenClaw on the same external `proxy` network.

4. Use a unique subdomain for each app:

```text
n8n.example.com
hermes-agent.example.com
openclaw.example.com
```

5. Point each subdomain DNS record to the VPS IP address.

6. Use the same Traefik certificate resolver name everywhere. If the Traefik command uses:

```yaml
--certificatesresolvers.mytlschallenge.acme.tlschallenge=true
```

Then app labels should use:

```yaml
traefik.http.routers.APP_NAME.tls.certresolver=mytlschallenge
```

7. Do not publish OpenClaw directly with `ports` unless there is a specific reason. Let Traefik reach it through Docker networking.

Example OpenClaw compose for a shared Traefik proxy:

```yaml
services:
  openclaw:
    image: ghcr.io/hostinger/hvps-openclaw:latest
    init: true
    restart: unless-stopped
    expose:
      - "${PORT}"
    labels:
      - traefik.enable=true
      - traefik.docker.network=proxy
      - traefik.http.routers.openclaw.rule=Host(`${OPENCLAW_HOST}`)
      - traefik.http.routers.openclaw.entrypoints=websecure
      - traefik.http.routers.openclaw.tls=true
      - traefik.http.routers.openclaw.tls.certresolver=mytlschallenge
      - traefik.http.services.openclaw.loadbalancer.server.port=${PORT}
    env_file:
      - .env
    volumes:
      - ./data:/data
      - ./data/linuxbrew:/home/linuxbrew
    networks:
      - proxy

networks:
  proxy:
    external: true
```

Example OpenClaw `.env`:

```env
PORT=61125
OPENCLAW_HOST=openclaw.example.com
TZ=Asia/Manila
OPENCLAW_GATEWAY_TOKEN=replace-with-a-strong-token
OPENAI_API_KEY=replace-with-your-api-key
TELEGRAM_BOT_TOKEN=optional-token
```

Restart OpenClaw:

```bash
docker compose down
docker compose up -d
```

Then open:

```text
https://openclaw.example.com
```

If the OpenClaw dashboard asks for a WebSocket URL, use:

```text
wss://openclaw.example.com
```

Quick checks:

```bash
docker ps
docker network inspect proxy
docker logs CONTAINER_NAME --tail=100
```

For Traefik issues, check the Traefik container logs. In many n8n-style Hostinger setups, the Traefik container is part of the n8n Docker project.

### Never commit real keys

Add local secret/config files to `.gitignore` if this repo will be public:

```gitignore
.env
*.local
*.secret
```

Global user config files under your home directory normally are not inside the repo, but project-level config files can be accidentally committed.

For actual setup on a fresh machine, still verify direct API access with `curl` before configuring any agent.

## References

- Xiaomi MiMo Token Plan Quick Access: https://platform.xiaomimimo.com/docs/en-US/tokenplan/quick-access
- Xiaomi AI Tools Overview: https://platform.xiaomimimo.com/docs/en-US/integration/tools-overview
- Xiaomi OpenCode Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/opencode
- Xiaomi OpenClaw Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/openclaw
- Xiaomi Claude Code Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/claudecode
- Xiaomi Hermes Agent Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/hermes-agent
- Xiaomi Kilo Code Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/kilocode
- Xiaomi Cline Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/cline
- Xiaomi Cherry Studio Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/cherrystudio
- Xiaomi Qwen Code Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/qwencode
- Xiaomi CodeBuddy Configuration: https://platform.xiaomimimo.com/docs/en-US/integration/codebuddy
- Kilo CLI docs: https://kilo.ai/docs/code-with-ai/platforms/cli
- OpenCode config docs: https://opencode.ai/docs/config/
- n8n Docker install docs: https://docs.n8n.io/hosting/installation/docker/
- n8n AI Agent docs: https://docs.n8n.io/integrations/builtin/cluster-nodes/root-nodes/n8n-nodes-langchain.agent/
- n8n OpenAI credentials docs: https://docs.n8n.io/integrations/builtin/credentials/openai/
