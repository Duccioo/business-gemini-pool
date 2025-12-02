# Business Gemini Pool Rotation

A Flask-based Google Gemini Enterprise API proxy service with support for multi-account rotation, OpenAI-compatible API, and a web management console.

## Project Structure

```
/
├── gemini.py                      # Backend service main program
├── index.html                     # Web management console frontend
├── business_gemini_session.json   # Configuration file
└── README.md                      # Project documentation
```

## Quick Request

### Send Chat Request

```bash
curl --location --request POST 'http://127.0.0.1:8000/v1/chat/completions' \
--header 'Content-Type: application/json' \
--data-raw '{
    "model": "gemini-enterprise-2",
    "messages": [
        {
            "role": "user",
            "content": "Hello"
        }
    ],
    "safe_mode": false
}'
```

## Features

### Core Features
- **Multi-Account Rotation**: Support for configuring multiple Gemini accounts with automatic rotation
- **OpenAI-Compatible API**: Provides API interface compatible with OpenAI format
- **Streaming Response**: Supports SSE (Server-Sent Events) streaming output
- **Proxy Support**: Supports HTTP/HTTPS proxy configuration
- **Automatic JWT Management**: Automatically obtains and refreshes JWT tokens

### Management Features
- **Web Console**: Beautiful web management interface with light/dark theme switching
- **Account Management**: Add, edit, delete, enable/disable accounts
- **Model Configuration**: Customize model parameter configuration
- **Proxy Testing**: Online testing of proxy connection status
- **Configuration Import/Export**: Support for importing and exporting configuration files

## File Description Description

### gemini.py

Backend service main program, developed based on Flask framework.

#### Main Classes and Functions

| Name | Type | Description |
|------|------|-------------|
| `AccountManager` | Class | Account manager, responsible for loading, saving, status management, and rotation selection of accounts |
| `load_config()` | Method | Load accounts and configuration from config file |
| `save_config()` | Method | Save configuration to file |
| `get_next_account()` | Method | Get next available account through rotation |
| `mark_account_unavailable()` | Method | Mark account as unavailable |
| `create_jwt()` | Function | Create JWT Token |
| `create_chat_session()` | Function | Create chat session |
| `stream_chat()` | Function | Send chat request and get response |
| `check_proxy()` | Function | Check if proxy is available |

#### API Endpoints

**OpenAI-Compatible Endpoints**

| Method | Path | Description |
|--------|------|-------------|
| GET | `/v1/models` | Get available model list |
| POST | `/v1/chat/completions` | Chat completion endpoint (supports images) |
| POST | `/v1/files` | Upload file |
| GET | `/v1/files` | Get file list |
| GET | `/v1/files/<id>` | Get file information |
| DELETE | `/v1/files/<id>` | Delete file |
| GET | `/v1/status` | Get system status |
| GET | `/health` | Health check |
| GET | `/image/<filename>` | Get cached image |

**Management Endpoints**

| Method | Path | Description |
|--------|------|-------------|
| GET | `/` | Return management page |
| GET | `/api/accounts` | Get account list |
| POST | `/api/accounts` | Add account |
| PUT | `/api/accounts/<id>` | Update account |
| DELETE | `/api/accounts/<id>` | Delete account |
| POST | `/api/accounts/<id>/toggle` | Toggle account status |
| POST | `/api/accounts/<id>/test` | Test account JWT acquisition |
| GET | `/api/models` | Get model configuration |
| POST | `/api/models` | Add model |
| PUT | `/api/models/<id>` | Update model |
| DELETE | `/api/models/<id>` | Delete model |
| GET | `/api/config` | Get complete configuration |
| PUT | `/api/config` | Update configuration |
| POST | `/api/config/import` | Import configuration |
| GET | `/api/config/export` | Export configuration |
| POST | `/api/proxy/test` | Test proxy |
| GET | `/api/proxy/status` | Get proxy status |

### business_gemini_session.json

Configuration file in JSON format, containing the following fields:

```json
{
    "proxy": "http://127.0.0.1:7890",
    "proxy_enabled": false,
    "accounts": [
        {
            "team_id": "Team ID",
            "secure_c_ses": "Secure Session Cookie",
            "host_c_oses": "Host Cookie",
            "csesidx": "Session Index",
            "user_agent": "Browser UA",
            "available": true
        }
    ],
    "models": [
        {
            "id": "Model ID",
            "name": "Model Name",
            "description": "Model Description",
            "context_length": 32768,
            "max_tokens": 8192,
            "price_per_1k_tokens": 0.0015
        }
    ]
}
```

#### Configuration Field Description

| Field | Type | Description |
|-------|------|-------------|
| `proxy` | string | HTTP proxy address |
| `proxy_enabled` | boolean | Proxy switch, `true` enables proxy, `false` disables proxy (default `false`) |
| `accounts` | array | Account list |
| `accounts[].team_id` | string | Google Cloud team ID |
| `accounts[].secure_c_ses` | string | Secure session Cookie |
| `accounts[].host_c_oses` | string | Host Cookie |
| `accounts[].csesidx` | string | Session index |
| `accounts[].user_agent` | string | Browser User-Agent |
| `accounts[].available` | boolean | Whether account is available |
| `models` | array | Model configuration list |
| `models[].id` | string | Model unique identifier |
| `models[].name` | string | Model display name |
| `models[].description` | string | Model description |
| `models[].context_length` | number | Context length limit |
| `models[].max_tokens` | number | Maximum output token count |

### index.html

Web management console frontend, single-file HTML application.

#### Feature Modules

1. **Dashboard**: Display system overview, account statistics, proxy status
2. **Account Management**: Add, delete, update, query accounts, status switching, JWT testing
3. **Model Configuration**: Add, delete, update, query models
4. **System Settings**: Proxy configuration, configuration import/export

#### Interface Features

- Responsive design, adapted to different screen sizes
- Support for light/dark theme switching
- Google Material Design style
- Real-time status updates

## Quick Start

### Requirements

- Python 3.7+
- Flask
- requests

### Method 1: Direct Execution

#### Install Dependencies

```bash
pip install flask requests flask-cors
```

#### Configure Account

Edit the `business_gemini_session.json` file and add your Gemini account information:

```json
{
    "proxy": "http://your-proxy:port",
    "proxy_enabled": true,
    "accounts": [
        {
            "team_id": "your-team-id",
            "secure_c_ses": "your-secure-c-ses",
            "host_c_oses": "your-host-c-oses",
            "csesidx": "your-csesidx",
            "user_agent": "Mozilla/5.0 ...",
            "available": true
        }
    ],
    "models": []
}
```

#### Start Service

```bash
python gemini.py
```

The service will start at `http://127.0.0.1:8000`.

### Method 2: Using Docker Compose

After manually creating `business_gemini_session.json` in the project directory:

Start with the command:

```bash
docker-compose up -d
```

### Access Management Console

- Direct execution: `http://127.0.0.1:8000/`
- Docker deployment: `http://127.0.0.1:8000/`

## API Usage Examplese Examples

### Get Model List

```bash
curl http://127.0.0.1:8000/v1/models
```

### Chat Completion

```bash
curl -X POST http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini-enterprise",
    "messages": [
      {"role": "user", "content": "Hello!"}
    ],
    "stream": false
  }'
```

### Streaming Chat

```bash
curl -X POST http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini-enterprise",
    "messages": [
      {"role": "user", "content": "Hello!"}
    ],
    "stream": true
  }'
```

### Chat with Images

Two methods are supported for sending images:

#### Method 1: Upload file first, then reference file_id first, then reference file_id

```bash
# 1. Upload image
curl -X POST http://127.0.0.1:8000/v1/files \
  -F "file=@image.png" \
  -F "purpose=assistants"
# Returns: {"id": "file-xxx", ...}

# 2. Reference file_id to send message
curl -X POST http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini-enterprise",
    "messages": [
      {
        "role": "user",
        "content": [
          {"type": "text", "text": "Describe this image"},
          {"type": "file", "file_id": "file-xxx"}
        ]
      }
    ]
  }'
```

#### Method 2: Inline base64 image (automatic upload)

**OpenAI Standard Format**

```bash
curl -X POST http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini-enterprise",
    "messages": [
      {
        "role": "user",
        "content": [
          {"type": "text", "text": "Describe this image"},
          {"type": "image_url", "image_url": {"url": "data:image/png;base64,..."}}
        ]
      }
    ]
  }'
```

**prompts Format (files array)**

```bash
curl -X POST http://127.0.0.1:8000/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gemini-enterprise",
    "prompts": [
      {
        "role": "user",
        "text": "Describe this image",
        "files": [
          {
            "data": "data:image/png;base64,...",
            "type": "image"
          }
        ]
      }
    ]
  }'
```

> **Note**: Inline base64 images are automatically uploaded to Gemini to obtain the fileId before sending the request.loaded to Gemini to obtain the fileId before sending the request.

## Important Notes

1. **Security**: The configuration file contains sensitive information, please keep it safe and do not commit it to public repositories
2. **Proxy**: If you need to access Google services, you may need to configure a proxy
3. **Account Restrictions**: Please comply with Google's terms of service and use the API responsibly
4. **JWT Expiration**: JWT tokens have a limited validity period, the system will automatically refresh them

## License

MIT License
