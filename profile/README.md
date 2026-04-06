QuillPHP is a PHP API framework with a Rust core — [**quill-core**](https://github.com/quillphp/quill-core) owns TCP, routing, validation, and JSON serialisation. 

PHP handles your handler logic. Nothing else.

**133,627 req/s** on an M-series machine. Go Fiber territory, in PHP.

---

## Architecture

```
Client → Axum/Tokio → matchit router → ValidatorRegistry → PHP handler → sonic-rs JSON → Client
```

- Rust handles every I/O layer (connections, routing, DTO validation, serialisation)
- PHP is forked at boot via `pcntl_fork`, never owns a socket
- Invalid requests are rejected in Rust before PHP is ever invoked
- Zero shared state across workers

---

## Quick Start

```bash
composer create-project quillphp/quill my-api && cd my-api
QUILL_WORKERS=4 php -d ffi.enable=on bin/quill serve --port=8080
```

```php
// routes.php
$app->get('/users',  [ListUsersAction::class,  '__invoke']);
$app->post('/users', [CreateUserAction::class, '__invoke']);
```

```php
class CreateUserDTO extends DTO
{
    #[Required, MinLength(2)] public readonly string $name;
    #[Required, Email]        public readonly string $email;
    // Validated in Rust. PHP never sees an invalid payload.
}
```

---

## Packages

| Package | Description |
|---|---|
| [`quill`](https://github.com/quillphp/quill) | Framework core |
| [`quill-core`](https://github.com/quillphp/quill-core) | Rust runtime (Axum, matchit, sonic-rs, FFI) |
| [`quill-cors`](https://github.com/quillphp/quill-cors) | CORS |
| [`quill-limiter`](https://github.com/quillphp/quill-limiter) | Rate limiting |
| [`quill-helmet`](https://github.com/quillphp/quill-helmet) | Security headers |
| [`quill-key-auth`](https://github.com/quillphp/quill-key-auth) | API key auth |
| [`quill-casbin`](https://github.com/quillphp/quill-casbin) | RBAC / ABAC |
| [`quill-otel`](https://github.com/quillphp/quill-otel) | OpenTelemetry tracing |
| [`quill-versioning`](https://github.com/quillphp/quill-versioning) | API versioning |
| [`quill-storage-redis`](https://github.com/quillphp/quill-storage-redis) | Redis driver |

[**Packagist**](https://packagist.org/packages/quillphp/quill) · [**quillphp.com**](https://quillphp.com)
