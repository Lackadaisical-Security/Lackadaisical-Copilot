# API Reference

## 📡 Overview

**Lackadaisical Local Copilot** provides a comprehensive RESTful API and WebSocket interface for all functionality. The API is designed with security, performance, and ease of use in mind.

## 🔐 Authentication

### API Token Authentication
```javascript
// Include API token in headers
const headers = {
  'Authorization': `Bearer ${API_TOKEN}`,
  'Content-Type': 'application/json'
};
```

### Session-Based Authentication
```javascript
// Session cookies automatically handled by browser
fetch('/api/endpoint', {
  credentials: 'include'
});
```

## 🚀 Base URL

- **Development**: `http://localhost:3000`
- **Production**: `https://yourdomain.com`

## 📋 Common Headers

```javascript
{
  'Content-Type': 'application/json',
  'Authorization': 'Bearer YOUR_API_TOKEN',
  'X-API-Version': '1.0'
}
```

## 🔄 Response Format

All API responses follow a consistent format:

```javascript
{
  "success": true,
  "data": {...},
  "message": "Operation completed successfully",
  "timestamp": "2024-12-19T10:30:00Z"
}
```

Error responses:
```javascript
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input parameters",
    "details": {...}
  },
  "timestamp": "2024-12-19T10:30:00Z"
}
```

---

## 💬 Chat API

### POST /api/chat/send
Send a message to the AI assistant.

**Request:**
```javascript
{
  "message": "Hello, how are you?",
  "conversationId": "conv_123",
  "model": "ollama:llama3.1",
  "temperature": 0.7,
  "maxTokens": 4096
}
```

**Response:**
```javascript
{
  "success": true,
  "data": {
    "response": "Hello! I'm doing well, thank you for asking...",
    "conversationId": "conv_123",
    "messageId": "msg_456",
    "model": "ollama:llama3.1",
    "tokensUsed": 45,
    "processingTime": 1230
  }
}
```

### GET /api/chat/conversations
Get all conversations.

**Response:**
```javascript
{
  "success": true,
  "data": [
    {
      "id": "conv_123",
      "title": "General Discussion",
      "createdAt": "2024-12-19T10:00:00Z",
      "updatedAt": "2024-12-19T10:30:00Z",
      "messageCount": 15,
      "model": "ollama:llama3.1"
    }
  ]
}
```

### GET /api/chat/conversations/:id
Get a specific conversation.

**Response:**
```javascript
{
  "success": true,
  "data": {
    "id": "conv_123",
    "title": "General Discussion",
    "messages": [
      {
        "id": "msg_456",
        "role": "user",
        "content": "Hello, how are you?",
        "timestamp": "2024-12-19T10:30:00Z"
      },
      {
        "id": "msg_457",
        "role": "assistant",
        "content": "Hello! I'm doing well...",
        "timestamp": "2024-12-19T10:30:05Z"
      }
    ],
    "createdAt": "2024-12-19T10:00:00Z",
    "updatedAt": "2024-12-19T10:30:00Z"
  }
}
```

### DELETE /api/chat/conversations/:id
Delete a conversation.

**Response:**
```javascript
{
  "success": true,
  "message": "Conversation deleted successfully"
}
```

---

## 🧠 Memory API

### GET /api/memory/search
Search memories with filters.

**Query Parameters:**
- `q` (string): Search query
- `type` (string): Memory type (conversation, knowledge, context, preferences)
- `importance` (number): Minimum importance score
- `limit` (number): Maximum results (default: 50)
- `offset` (number): Results offset (default: 0)

**Example:**
```
GET /api/memory/search?q=javascript&type=knowledge&importance=0.7&limit=10
```

**Response:**
```javascript
{
  "success": true,
  "data": {
    "memories": [
      {
        "id": "mem_789",
        "type": "knowledge",
        "content": "JavaScript is a programming language...",
        "importance": 0.8,
        "createdAt": "2024-12-19T10:00:00Z",
        "lastAccessed": "2024-12-19T10:30:00Z",
        "tags": ["javascript", "programming"]
      }
    ],
    "total": 25,
    "hasMore": true
  }
}
```

### POST /api/memory/store
Store a new memory.

**Request:**
```javascript
{
  "content": "JavaScript is a versatile programming language...",
  "type": "knowledge",
  "importance": 0.8,
  "tags": ["javascript", "programming"],
  "metadata": {
    "source": "user_input",
    "context": "learning_session"
  }
}
```

**Response:**
```javascript
{
  "success": true,
  "data": {
    "id": "mem_789",
    "type": "knowledge",
    "content": "JavaScript is a versatile programming language...",
    "importance": 0.8,
    "createdAt": "2024-12-19T10:30:00Z"
  }
}
```

### GET /api/memory/:id
Get a specific memory.

**Response:**
```javascript
{
  "success": true,
  "data": {
    "id": "mem_789",
    "type": "knowledge",
    "content": "JavaScript is a versatile programming language...",
    "importance": 0.8,
    "createdAt": "2024-12-19T10:00:00Z",
    "lastAccessed": "2024-12-19T10:30:00Z",
    "tags": ["javascript", "programming"],
    "metadata": {
      "source": "user_input",
      "context": "learning_session"
    }
  }
}
```

### DELETE /api/memory/:id
Delete a memory.

**Response:**
```javascript
{
  "success": true,
  "message": "Memory deleted successfully"
}
```

### POST /api/memory/cleanup
Trigger memory cleanup.

**Request:**
```javascript
{
  "maxAge": 2592000000,
  "minImportance": 0.3,
  "preserveCount": 1000
}
```

**Response:**
```javascript
{
  "success": true,
  "data": {
    "memoriesDeleted": 45,
    "memoriesPreserved": 955,
    "cleanupTime": 1230
  }
}
```

---

## ⚙️ Configuration API

### GET /api/config
Get current configuration.

**Response:**
```javascript
{
  "success": true,
  "data": {
    "agent": {
      "name": "Lackadaisical Assistant",
      "traits": ["helpful", "knowledgeable", "privacy-focused"],
      "temperature": 0.7
    },
    "memory": {
      "ephemeralMaxSize": 1048576,
      "importanceThreshold": 0.3,
      "maxAge": 2592000000
    },
    "models": {
      "ollama": {
        "baseUrl": "http://localhost:11434",
        "defaultModel": "llama3.1:8b"
      }
    }
  }
}
```

### POST /api/config/update
Update configuration.

**Request:**
```javascript
{
  "agent": {
    "temperature": 0.8,
    "maxTokens": 4096
  },
  "memory": {
    "importanceThreshold": 0.4
  }
}
```

**Response:**
```javascript
{
  "success": true,
  "message": "Configuration updated successfully"
}
```

### POST /api/config/reset
Reset configuration to defaults.

**Response:**
```javascript
{
  "success": true,
  "message": "Configuration reset to defaults"
}
```

---

## 🔄 System API

### GET /api/system/status
Get system status.

**Response:**
```javascript
{
  "success": true,
  "data": {
    "status": "healthy",
    "uptime": 3600000,
    "version": "1.0.0-rc",
    "environment": "production",
    "database": {
      "status": "connected",
      "size": 1048576,
      "backupCount": 5
    },
    "memory": {
      "usage": 52428800,
      "limit": 104857600,
      "memoryCount": 1250
    },
    "models": {
      "ollama": "connected",
      "openai": "available",
      "anthropic": "available"
    }
  }
}
```

### GET /api/system/metrics
Get system metrics.

**Response:**
```javascript
{
  "success": true,
  "data": {
    "requests": {
      "total": 1250,
      "successful": 1230,
      "failed": 20,
      "avgResponseTime": 45
    },
    "websocket": {
      "connections": 15,
      "messages": 2500,
      "avgLatency": 8
    },
    "database": {
      "queries": 5000,
      "avgQueryTime": 2.5,
      "slowQueries": 12
    },
    "memory": {
      "totalMemories": 1250,
      "averageImportance": 0.65,
      "storageUsed": 10485760
    }
  }
}
```

### GET /api/system/health
Health check endpoint.

**Response:**
```javascript
{
  "success": true,
  "data": {
    "status": "healthy",
    "timestamp": "2024-12-19T10:30:00Z",
    "checks": {
      "database": "pass",
      "memory": "pass",
      "models": "pass",
      "websocket": "pass"
    }
  }
}
```

---

## 📁 Export/Import API

### POST /api/export/conversations
Export conversations.

**Request:**
```javascript
{
  "format": "json",
  "conversationIds": ["conv_123", "conv_456"],
  "includeMetadata": true
}
```

**Response:**
```javascript
{
  "success": true,
  "data": {
    "exportId": "export_789",
    "downloadUrl": "/api/export/download/export_789",
    "format": "json",
    "size": 1048576,
    "expiresAt": "2024-12-19T11:30:00Z"
  }
}
```

### POST /api/export/memories
Export memories.

**Request:**
```javascript
{
  "format": "json",
  "filters": {
    "type": "knowledge",
    "importance": 0.7
  },
  "includeMetadata": true
}
```

### POST /api/import/conversations
Import conversations.

**Request (multipart/form-data):**
```javascript
const formData = new FormData();
formData.append('file', file);
formData.append('format', 'json');
formData.append('overwrite', 'false');
```

**Response:**
```javascript
{
  "success": true,
  "data": {
    "importId": "import_123",
    "conversationsImported": 15,
    "memoriesImported": 250,
    "status": "completed"
  }
}
```

---

## 🔍 Search API

### GET /api/search/web
Web search integration.

**Query Parameters:**
- `q` (string): Search query
- `limit` (number): Maximum results (default: 10)
- `safe` (boolean): Safe search (default: true)

**Response:**
```javascript
{
  "success": true,
  "data": {
    "results": [
      {
        "title": "JavaScript Guide",
        "url": "https://example.com/js-guide",
        "description": "Comprehensive JavaScript tutorial...",
        "relevance": 0.95
      }
    ],
    "total": 1000,
    "searchTime": 450
  }
}
```

### GET /api/search/memory
Memory search (same as /api/memory/search).

---

## 🔌 WebSocket API

### Connection
```javascript
const ws = new WebSocket('ws://localhost:3000');
// or for secure connection:
const ws = new WebSocket('wss://localhost:3000');
```

### Message Format
All WebSocket messages follow this format:
```javascript
{
  "type": "message_type",
  "id": "unique_message_id",
  "data": {...},
  "timestamp": "2024-12-19T10:30:00Z"
}
```

### Client to Server Events

#### chat_message
Send a chat message.
```javascript
{
  "type": "chat_message",
  "id": "msg_123",
  "data": {
    "message": "Hello, how are you?",
    "conversationId": "conv_456",
    "model": "ollama:llama3.1"
  }
}
```

#### memory_search
Search memories in real-time.
```javascript
{
  "type": "memory_search",
  "id": "search_123",
  "data": {
    "query": "javascript",
    "type": "knowledge",
    "limit": 10
  }
}
```

#### typing_start
Indicate user is typing.
```javascript
{
  "type": "typing_start",
  "id": "typing_123",
  "data": {
    "conversationId": "conv_456"
  }
}
```

#### typing_stop
Indicate user stopped typing.
```javascript
{
  "type": "typing_stop",
  "id": "typing_124",
  "data": {
    "conversationId": "conv_456"
  }
}
```

### Server to Client Events

#### chat_response
AI response to chat message.
```javascript
{
  "type": "chat_response",
  "id": "msg_123",
  "data": {
    "response": "Hello! I'm doing well...",
    "conversationId": "conv_456",
    "messageId": "msg_789",
    "model": "ollama:llama3.1",
    "tokensUsed": 45
  }
}
```

#### chat_stream
Streaming AI response (partial).
```javascript
{
  "type": "chat_stream",
  "id": "msg_123",
  "data": {
    "content": "Hello! I'm",
    "conversationId": "conv_456",
    "messageId": "msg_789",
    "isComplete": false
  }
}
```

#### memory_results
Memory search results.
```javascript
{
  "type": "memory_results",
  "id": "search_123",
  "data": {
    "memories": [...],
    "total": 25,
    "hasMore": true
  }
}
```

#### system_notification
System notifications.
```javascript
{
  "type": "system_notification",
  "id": "notif_123",
  "data": {
    "level": "info",
    "message": "Backup completed successfully",
    "category": "system"
  }
}
```

#### error
Error messages.
```javascript
{
  "type": "error",
  "id": "error_123",
  "data": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid message format",
    "details": {...}
  }
}
```

---

## 🔧 Error Codes

### Authentication Errors
- `AUTH_REQUIRED`: Authentication required
- `INVALID_TOKEN`: Invalid API token
- `TOKEN_EXPIRED`: API token expired
- `UNAUTHORIZED`: Insufficient permissions

### Validation Errors
- `VALIDATION_ERROR`: Input validation failed
- `MISSING_REQUIRED`: Required field missing
- `INVALID_FORMAT`: Invalid data format
- `OUT_OF_RANGE`: Value out of acceptable range

### System Errors
- `INTERNAL_ERROR`: Internal server error
- `DATABASE_ERROR`: Database operation failed
- `MODEL_UNAVAILABLE`: AI model not available
- `RATE_LIMITED`: Rate limit exceeded

### Memory Errors
- `MEMORY_NOT_FOUND`: Memory not found
- `MEMORY_LIMIT_EXCEEDED`: Memory storage limit exceeded
- `INVALID_MEMORY_TYPE`: Invalid memory type

---

## 📊 Rate Limiting

### Default Limits
- **API Requests**: 100 requests per 15 minutes
- **WebSocket Messages**: 1000 messages per minute
- **Large Operations**: 10 operations per hour

### Rate Limit Headers
```javascript
{
  'X-RateLimit-Limit': '100',
  'X-RateLimit-Remaining': '95',
  'X-RateLimit-Reset': '1640123456'
}
```

### Rate Limit Exceeded Response
```javascript
{
  "success": false,
  "error": {
    "code": "RATE_LIMITED",
    "message": "Rate limit exceeded",
    "details": {
      "limit": 100,
      "remaining": 0,
      "resetTime": "2024-12-19T10:45:00Z"
    }
  }
}
```

---

## 🛡️ Security

### HTTPS Configuration
All production deployments should use HTTPS:
```javascript
const baseUrl = 'https://yourdomain.com';
const wsUrl = 'wss://yourdomain.com';
```

### API Token Security
- Store API tokens securely
- Use environment variables
- Rotate tokens regularly
- Never expose tokens in client-side code

### Input Validation
All inputs are validated:
- XSS prevention
- SQL injection protection
- Size limits enforced
- Type checking

---

## 📝 Usage Examples

### Complete Chat Example
```javascript
// Initialize WebSocket connection
const ws = new WebSocket('ws://localhost:3000');

// Send chat message
ws.send(JSON.stringify({
  type: 'chat_message',
  id: 'msg_' + Date.now(),
  data: {
    message: 'Hello, how are you?',
    conversationId: 'conv_123',
    model: 'ollama:llama3.1'
  }
}));

// Handle response
ws.onmessage = (event) => {
  const message = JSON.parse(event.data);
  
  if (message.type === 'chat_response') {
    console.log('AI Response:', message.data.response);
  } else if (message.type === 'chat_stream') {
    console.log('Streaming:', message.data.content);
  }
};
```

### Memory Search Example
```javascript
// Search memories via API
const searchMemories = async (query) => {
  const response = await fetch(`/api/memory/search?q=${encodeURIComponent(query)}&limit=10`, {
    headers: {
      'Authorization': `Bearer ${API_TOKEN}`
    }
  });
  
  const result = await response.json();
  return result.data.memories;
};

// Use the function
const memories = await searchMemories('javascript programming');
console.log('Found memories:', memories);
```

### Configuration Update Example
```javascript
// Update configuration
const updateConfig = async (newConfig) => {
  const response = await fetch('/api/config/update', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${API_TOKEN}`
    },
    body: JSON.stringify(newConfig)
  });
  
  return await response.json();
};

// Update agent temperature
const result = await updateConfig({
  agent: {
    temperature: 0.8
  }
});
```

---

## 🔄 API Versioning

### Version Header
```javascript
{
  'X-API-Version': '1.0'
}
```

### Version in URL (future)
```
/api/v1/chat/send
/api/v2/chat/send
```

---

## 📚 Additional Resources

- [Getting Started Guide](README.md#quick-start)
- [Security Policy](SECURITY.md)
- [System Analysis](SYSTEM_ANALYSIS.md)
- [Implementation Guide](implementation_guide.md)

---

## 🤝 Support

For API support and questions:
- **Documentation**: Check this API reference
- **Issues**: Report on GitHub
- **Community**: Join discussions
- **Email**: `api-support@lackadaisical-copilot.dev`

---

**API Version**: 1.0.0-rc  
**Last Updated**: December 19, 2024  
**Test Coverage**: 89% (API endpoints validated)

---

*"Great APIs make great applications. We've designed ours with developers in mind."* 📡 