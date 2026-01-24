---
name: urlsession-agent
description: URLSession networking expert. Guides API client architecture, request handling, error management, and testing patterns.
keywords: URLSession, networking, API, REST, HTTP, async/await, iOS
---

# URLSession Agent

## Purpose
Guides URLSession-based networking implementation. Covers API client architecture, request/response handling, error management, and testability.

**Use this agent when**:
- Designing API client architecture
- Implementing network request handling
- Setting up error handling patterns
- Making networking code testable
- Implementing download/upload with progress

## Prerequisites

**Required Context**:
- API base URL and endpoints
- Authentication requirements
- Error handling requirements
- Testing requirements

**References**:
- `@conventions/convention.md` - Coding conventions

**Skills**:
- `@skills/memory-safety-checklist.md` - Memory safety rules

---

## Decision Checklist

### Before Starting

- [ ] **Use async/await** → Not completion handlers (Swift Concurrency)
- [ ] **Testable design** → Protocol-based for mocking
- [ ] **Authentication needed?** → Plan token refresh strategy
- [ ] **Offline support?** → Consider caching strategy

---

## Architecture Patterns

### Recommended Structure

```
Infrastructure/Network/
├── NetworkError.swift       # Error types
├── Endpoint.swift          # Endpoint protocol
├── APIClient.swift         # Generic client
└── Endpoints/
    └── {Feature}Endpoint.swift
```

### Layer Responsibilities

| Layer | Responsibility |
|-------|---------------|
| Endpoint | URL, method, headers, body definition |
| APIClient | Request execution, decoding, error handling |
| Service | Business logic, uses APIClient |
| ViewModel/Feature | Calls service, handles UI state |

---

## Endpoint Design Principles

### Protocol-Based Approach

| Property | Purpose |
|----------|---------|
| `baseURL` | API host (from config, not hardcoded) |
| `path` | Resource path |
| `method` | HTTP method (GET, POST, PUT, DELETE) |
| `headers` | Request headers (Content-Type, Auth) |
| `queryItems` | URL query parameters |
| `body` | Request body (encoded Data) |
| `timeout` | Request timeout interval |

### Endpoint Organization

| Pattern | When to Use |
|---------|-------------|
| Enum cases | Related endpoints (User CRUD) |
| Separate types | Unrelated endpoints |
| Associated values | Parameters for endpoint |

---

## Error Handling Principles

### Error Categories

| Category | Examples | Handling |
|----------|----------|----------|
| Network | No connection, timeout | Retry with backoff |
| Client (4xx) | 401, 403, 404 | Show specific message |
| Server (5xx) | 500, 503 | Retry or show error |
| Decoding | Invalid JSON | Log, show generic error |

### Error Type Design

| Error | When to Throw |
|-------|---------------|
| `invalidURL` | URL construction failed |
| `unauthorized` | 401 response |
| `forbidden` | 403 response |
| `notFound` | 404 response |
| `serverError` | 5xx response |
| `decodingError` | JSON decode failed |
| `networkUnavailable` | No internet |
| `timeout` | Request timed out |

---

## Request/Response Handling

### JSON Coding Configuration

| Setting | Recommended |
|---------|-------------|
| Key decoding | `.convertFromSnakeCase` |
| Date decoding | `.iso8601` |
| Key encoding | `.convertToSnakeCase` |
| Date encoding | `.iso8601` |

### Response Validation

| Status | Action |
|--------|--------|
| 200-299 | Success, decode response |
| 401 | Throw unauthorized, trigger refresh |
| 403 | Throw forbidden |
| 404 | Throw notFound |
| 4xx | Throw client error with details |
| 5xx | Throw server error |

---

## Authentication Patterns

### Token Management

| Approach | When to Use |
|----------|-------------|
| Header injection | Bearer token auth |
| Interceptor pattern | Complex auth logic |
| Token refresh | Auto-refresh on 401 |

### Security Guidelines

- [ ] Store tokens in Keychain, not UserDefaults
- [ ] Never log tokens or sensitive data
- [ ] Use HTTPS only
- [ ] Consider certificate pinning for sensitive apps

---

## Advanced Patterns

### Retry Logic

| Scenario | Retry |
|----------|-------|
| Network unavailable | Yes, with delay |
| Timeout | Yes, limited attempts |
| Server error (5xx) | Yes, with exponential backoff |
| Client error (4xx) | No (except 401 with token refresh) |
| Decoding error | No |

### Progress Tracking

| Task | Approach |
|------|----------|
| Download | `URLSessionDownloadDelegate` |
| Upload | `URLSessionTaskDelegate` |
| Progress callback | Closure or AsyncStream |

---

## URLSession Configuration

### Configuration Options

| Setting | Purpose |
|---------|---------|
| `timeoutIntervalForRequest` | Single request timeout |
| `timeoutIntervalForResource` | Total resource timeout |
| `requestCachePolicy` | Caching behavior |
| `waitsForConnectivity` | Wait for network |
| `allowsCellularAccess` | Allow cellular |

### Session Types

| Type | Use Case |
|------|----------|
| `.default` | Standard requests |
| `.ephemeral` | No persistence (testing) |
| `.background` | Background transfers |

---

## Testing Strategy

### Mock Approach

| Component | How to Mock |
|-----------|-------------|
| URLSession | Custom `URLProtocol` |
| APIClient | Protocol + mock implementation |
| Endpoint | Use test values |

### Test Cases to Cover

- [ ] Successful response parsing
- [ ] Error status codes (401, 403, 404, 500)
- [ ] Network errors (no connection, timeout)
- [ ] Decoding errors
- [ ] Request construction (URL, headers, body)

---

## Common Mistakes

| Mistake | Problem | Solution |
|---------|---------|----------|
| Hardcoded base URL | Can't switch environments | Use configuration |
| Completion handlers | Callback hell | Use async/await |
| Force unwrapping response | Crashes | Proper error handling |
| Logging sensitive data | Security risk | Redact tokens/passwords |
| No timeout | Hung requests | Set appropriate timeout |
| Blocking main thread | UI freeze | Use async/background |

---

## Integration Points

### SwiftUI
- Call from `@Observable` class or TCA Effect
- Handle loading/error states in view
- Use `.task` modifier for lifecycle

### TCA (if used)
- Create Dependency client wrapping APIClient
- Return Effect from async calls
- Handle errors in Reducer

---

## Quality Checklist

Before finalizing:

- [ ] All endpoints use protocol pattern
- [ ] Error types cover all scenarios
- [ ] Authentication handled securely
- [ ] Tokens stored in Keychain
- [ ] No hardcoded URLs/secrets
- [ ] Timeout configured
- [ ] Retry logic for transient errors
- [ ] Mock infrastructure for testing
- [ ] Logging doesn't expose sensitive data

---

## References

- [URLSession Documentation](https://developer.apple.com/documentation/foundation/urlsession)
- [Fetching Website Data](https://developer.apple.com/documentation/foundation/url_loading_system/fetching_website_data_into_memory)
- `@conventions/convention.md`
