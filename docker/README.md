# Docker Container Monitoring

## Task 1 — Deploy Sample Containers

Three containers were deployed to represent a web server, a cache, and a database:

```bash
docker run -d --name web1 -p 8081:80 nginx
docker run -d --name cache1 -p 6379:6379 redis
docker run -d --name db1 -e POSTGRES_PASSWORD=pass -p 5432:5432 postgres
```

To make resource usage observable, `web1` was given an artificial CPU load:

```bash
docker exec -it web1 sh -c "yes > /dev/null &"
```

## Task 2 — Monitor Containers with `docker stats`

All containers at once:

```bash
docker stats
```

A single container:

```bash
docker stats web1
```

These commands were used to identify:

- **CPU usage** — percentage of host CPU consumed
- **Memory usage / limit** — current memory vs. the container’s memory ceiling
- **Network I/O** — bytes sent/received over the container’s network interface
- **Block I/O** — bytes read/written to disk
- **Number of processes (PIDs)** — how many processes are running inside the container

Screenshots of this output are saved in `../screenshots/docker-stats/`.

## Why this matters

`docker stats` is the fastest way to catch a container that is silently consuming too much CPU
or leaking memory before it takes down the host or its neighbors. It’s a live snapshot, not a
history — that gap is what Prometheus + Grafana solve later in this project.
