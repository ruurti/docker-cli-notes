# Docker Swarm — Học từ đầu

> Lộ trình học Swarm từ zero đến production.  
> Đọc theo thứ tự, thực hành luôn sau mỗi bài.  
> Quay lại: [../README.md](../README.md) · Cheat sheet: [../README-swarm.md](../README-swarm.md)

---

## Lộ trình

| # | File | Nội dung | Thực hành |
| - | ---- | -------- | --------- |
| 0 | [00-concepts.md](00-concepts.md) | Swarm là gì, Manager/Worker, Raft, thuật ngữ | Đọc hiểu — không cần máy |
| 1 | [01-setup.md](01-setup.md) | Dựng cluster local: single-node hoặc 3-node DinD | Có cluster chạy được |
| 2 | [02-first-service.md](02-first-service.md) | Deploy service, scale, quan sát self-healing | Kill container, xem Swarm tự tạo lại |
| 3 | [03-networking.md](03-networking.md) | Overlay network, DNS, VIP, routing mesh | 2 service gọi nhau qua tên |
| 4 | [04-stack.md](04-stack.md) | `deploy:` block, stack deploy từ compose.yaml | Deploy web + db stack hoàn chỉnh |
| 5 | [05-secrets.md](05-secrets.md) | Docker Secret & Config, rotate, inject vào service | Deploy postgres với secret password |
| 6 | [06-updates.md](06-updates.md) | Rolling update, failure_action, rollback | Update image, giả lập lỗi, tự rollback |
| 7 | [07-production.md](07-production.md) | Node labels, resource limits, checklist production | Production-ready stack YAML |
| 8 | [08-ingress.md](08-ingress.md) | Traffic từ ngoài vào: routing mesh + Traefik + HTTPS | Deploy Traefik, route domain/path, SSL |

---

## Gợi ý lộ trình theo mục tiêu

### Muốn hiểu Swarm là gì (1 tiếng)

```text
00-concepts → 01-setup (Option A) → 02-first-service
```

### Muốn deploy app thực tế lên Swarm (nửa ngày)

```text
00-concepts → 01-setup → 02-first-service → 03-networking → 04-stack → 05-secrets
```

### Muốn đưa lên production thực sự (cả ngày)

```text
Toàn bộ 00 → 08 theo thứ tự
```

---

## Concepts cần nhớ trước khi bắt đầu

```text
Node      = 1 máy trong cluster (Manager hoặc Worker)
Service   = định nghĩa "chạy image X với N bản"
Task      = 1 bản chạy cụ thể của service (= 1 container trên 1 node)
Stack     = nhóm service liên quan, deploy từ 1 file compose.yaml
Overlay   = network ảo trải rộng nhiều host, container gọi nhau qua tên service
Ingress   = routing mesh built-in + Traefik để route L7 từ ngoài vào
```
