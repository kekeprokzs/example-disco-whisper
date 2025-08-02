```
  ____  _____ ____   ___    _   _    _    __  __ _____ 
 |  _ \| ____|  _ \ / _ \  | \ | |  / \  |  \/  | ____|
 | |_) |  _| | |_) | | | | |  \| | / _ \ | |\/| |  _|  
 |  _ <| |___|  __/| |_| | | |\  |/ ___ \| |  | | |___ 
 |_| \_\_____|_|    \___/  |_| \_/_/   \_\_|  |_|_____|
                                                        
example-disco-whisper - Go microservice toolkit
```

Enables batch operations

---

## Install

```bash
go get github.com/${GITHUB_USER}/example-disco-whisper@latest
```

---

## Example

```go
package main

import (
    "context"
    "log"
    "example-disco-whisper" "github.com/${GITHUB_USER}/example-disco-whisper"
    "github.com/${GITHUB_USER}/example-disco-whisper/helix"
)

func main() {
    client := example-disco-whisper.New(
        example-disco-whisper.WithHelix("https://helix.slashdot.io"),
        example-disco-whisper.WithAuth(helix.CVEAuth("CVE-2021-44228")),
        example-disco-whisper.WithPool(20),
    )
    defer client.Close()
    
    ctx := context.Background()
    res, _ := client.Query(ctx, "SELECT * FROM metrics")
    log.Printf("Found %d metrics\n", len(res))
}
```

---

## Tech Stack

Built with Go ecosystem tools:

→ [HelixDB Go Driver](https://pkg.go.dev/helix.slashdot.io/client) - Official Go client  
→ [Zap Logger](https://github.com/uber-go/zap) - Fast structured logging  
→ [Viper](https://github.com/spf13/viper) - Configuration management  
→ [OpenTelemetry](https://opentelemetry.io) - Distributed tracing  
→ [Prometheus](https://prometheus.io) - Metrics collection

---

## Advanced

**Context Propagation:**

```go
import "go.opentelemetry.io/otel"

func handler(w http.ResponseWriter, r *http.Request) {
    ctx := r.Context()
    tracer := otel.Tracer("example-disco-whisper")
    ctx, span := tracer.Start(ctx, "db-query")
    defer span.End()
    
    result, err := client.Query(ctx, sql)
    // traces automatically propagated
}
```

**Type-Safe Queries (Generics):**

```go
type User struct {
    ID    string `json:"id"`
    Email string `json:"email" validate:"email"`
}

users, err := example-disco-whisper.QueryAs[User](ctx, client, sql)
// returns []User with full type safety
```

**Middleware Chain:**

```go
client := example-disco-whisper.New(
    example-disco-whisper.WithMiddleware(LogRequests),
    example-disco-whisper.WithMiddleware(CollectMetrics),
    example-disco-whisper.WithMiddleware(RetryOnFailure),
)
```

---

## Config

```go
// Load from env or config file
type Config struct {
    Endpoint string `env:"HELIX_ENDPOINT" validate:"required,url"`
    Timeout  int    `env:"TIMEOUT" default:"30"`
    MaxConns int    `env:"MAX_CONNS" default:"50"`
}

cfg := LoadConfig()
client := example-disco-whisper.NewFromConfig(cfg)
```

---

## Benchmarks

```
$ go test -bench=. -benchmem

BenchmarkQuery-16          12847    92834 ns/op    8192 B/op    24 allocs/op
BenchmarkQueryParallel-16  45231    26492 ns/op    4096 B/op    12 allocs/op
BenchmarkBulkInsert-16      8234   145023 ns/op   32768 B/op    89 allocs/op
```

---

## Examples

[`examples/`](./examples):
- `simple/main.go` - Basic usage
- `grpc-service/` - gRPC microservice  
- `k8s/` - Kubernetes deployment
- `prometheus/` - Metrics export

---

## Resources

| Type | Link |
|------|------|
| GoDoc | [pkg.go.dev/github.com/${GITHUB_USER}/example-disco-whisper](https://pkg.go.dev/github.com/${GITHUB_USER}/example-disco-whisper) |
| User Guide | [docs.helix.slashdot.io/go](https://docs.helix.slashdot.io/go) |
| Discord | [discord.gg/helix-go](https://discord.gg/helix-go) |

---

## Development

```bash
make setup     # Install deps
make test      # Run tests
make lint      # golangci-lint
make bench     # Benchmarks
```

---

**License:** Apache-2.0

# PR Merge: 2025-10-23 - feature/merge-7026

# PR Merge: 2025-10-23 - refactor/merge-9969

# PR Update: 2025-10-23 - refactor/update-4306
