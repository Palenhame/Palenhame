<!-- palenhame@prod:~$ systemctl status engineer -->

<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="./assets/daemon-header-dark.svg" />
    <source media="(prefers-color-scheme: light)" srcset="./assets/daemon-header-light.svg" />
    <img alt="palenhame@prod systemctl status" src="./assets/daemon-header-dark.svg" width="100%" />
  </picture>
</div>

<br />

### Architecture & Runtime Philosophy

```go
package prod

import (
	"context"
	"time"

	"golang.org/x/sync/errgroup"
)

// Engineer: concurrency, fast SQL, Redis read-through & isolated Docker containers
type Engineer struct {
	Core    string // Go & Python
	Cache   Cache  // Redis (LRU / Streams)
	Storage DB     // PostgreSQL (pgx / partitioning)
	Runtime string // Docker & Linux cgroups
}

func (e *Engineer) HandleLoad(ctx context.Context, req Request) (Response, error) {
	if cached, ok := e.Cache.Get(ctx, req.Key()); ok {
		return cached, nil
	}

	g, ctx := errgroup.WithContext(ctx)
	var res Response

	g.Go(func() error {
		row, err := e.Storage.QueryFast(ctx, req.SQL())
		if err != nil {
			return err
		}
		res = row
		return e.Cache.SetAsync(ctx, req.Key(), row, 5*time.Minute)
	})

	if err := g.Wait(); err != nil {
		return Response{}, err
	}
	return res, nil
}
```

<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="./assets/architecture-dark.svg" />
    <source media="(prefers-color-scheme: light)" srcset="./assets/architecture-light.svg" />
    <img alt="Backend topology diagram" src="./assets/architecture-dark.svg" width="100%" />
  </picture>
</div>

<br />

### Activity Stream

<div align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/Palenhame/Palenhame/output/github-contribution-grid-snake-dark.svg" />
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/Palenhame/Palenhame/output/github-contribution-grid-snake.svg" />
    <img alt="GitHub contribution grid snake animation" src="https://raw.githubusercontent.com/Palenhame/Palenhame/output/github-contribution-grid-snake-dark.svg" width="100%" />
  </picture>
</div>

<br />

### Endpoints

```bash
palenhame@prod:~$ ping telegram
PING telegram.org (@I_Nobody69): 56 data bytes
64 bytes from @I_Nobody69: icmp_seq=0 ttl=57 time=11.4 ms
-> https://t.me/I_Nobody69

palenhame@prod:~$ curl -I https://github.com/palenhame
HTTP/2 200 OK · Backend Systems Architect · 99.98% availability mindset
```
