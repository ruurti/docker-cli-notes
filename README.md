# Docker CLI — Index

Repo này là cheat sheet tổng hợp Docker CLI để tra cứu nhanh, không cần vào docs.

---

## Mở file nào?

| Tình huống | File |
| ---------- | ---- |
| Làm việc với container, image, network, volume | [README-core.md](README-core.md) |
| Viết hoặc đọc Dockerfile | [README-dockerfile.md](README-dockerfile.md) |
| Dùng Docker Compose (local dev / staging) | [README-compose.md](README-compose.md) |
| Vận hành Docker Swarm (production cluster) | [README-swarm.md](README-swarm.md) |

---

## Tổng quan các chủ đề

### [README-core.md](README-core.md)

Docker CLI cơ bản — nền tảng của mọi thứ còn lại.

- `docker container` — run, exec, logs, stop, rm
- `docker image` — build, pull, push, tag, buildx
- `docker network` — bridge, overlay, connect
- `docker volume` — named volume, bind mount
- `docker system` — df, prune, info
- Workflow tag & push lên private registry

### [README-dockerfile.md](README-dockerfile.md)

Viết Dockerfile đúng cách.

- Tất cả instruction: FROM, RUN, COPY, CMD, ENTRYPOINT, ENV, ARG, WORKDIR...
- CMD vs ENTRYPOINT — khi nào dùng cái nào
- ARG vs ENV — build-time vs runtime
- Multi-stage build — giảm kích thước image production
- Best practices: layer cache, non-root user, .dockerignore

### [README-compose.md](README-compose.md)

Chạy nhiều container cùng nhau bằng `compose.yaml`.

- Vòng đời: up, down, logs, exec, restart
- Cấu trúc `compose.yaml` — các key quan trọng
- `depends_on` với health condition
- `.env` và variable interpolation
- `docker compose watch` — hot-reload khi dev
- Patterns: override file, migration, multi-env

### [README-swarm.md](README-swarm.md)

Orchestration production trên nhiều host.

- Khởi tạo cluster, join node
- Quản lý node: drain, promote, demote
- Service: create, scale, rolling update, rollback
- Stack: deploy từ compose.yaml
- Secret & Config — lưu trữ an toàn trong Raft
- Overlay network — kết nối cross-host

---

## Chọn công cụ nào?

```text
Chạy 1 container đơn giản?
  └─> docker container run  →  README-core.md

Chạy nhiều service cùng nhau ở local / staging?
  └─> Docker Compose        →  README-compose.md

Triển khai production trên nhiều server, cần HA?
  └─> Docker Swarm          →  README-swarm.md
```
