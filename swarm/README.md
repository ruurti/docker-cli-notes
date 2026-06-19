# Docker Swarm — Học từ đầu

> Lộ trình học Swarm theo thứ tự. Đọc từng file theo số thứ tự, thực hành luôn sau mỗi file.  
> Quay lại: [../README.md](../README.md) (index tổng)

---

## Lộ trình

| File | Nội dung | Thực hành |
| ---- | -------- | --------- |
| [00-concepts.md](00-concepts.md) | Swarm là gì, architecture, thuật ngữ | Không — đọc hiểu |
| [01-setup.md](01-setup.md) | Dựng cluster local (1 node hoặc 3 node DinD) | Có cluster chạy được |
| [02-first-service.md](02-first-service.md) | Deploy service đầu tiên, scale, self-healing | Deploy nginx, kill container xem tự recover |
| [03-networking.md](03-networking.md) | Overlay network, DNS, VIP, routing mesh | 2 service nói chuyện qua tên |
| [04-stack.md](04-stack.md) | compose.yaml với `deploy:` block, stack deploy | Deploy web + db stack hoàn chỉnh |
| [05-secrets.md](05-secrets.md) | Secret & Config, rotate, dùng trong stack | Deploy postgres với secret password |
| [06-updates.md](06-updates.md) | Rolling update, rollback, failure_action | Update image, giả lập lỗi, rollback |
| [07-production.md](07-production.md) | Node labels, resource limits, checklist | Đọc + áp dụng khi deploy thực tế |
| [08-ingress.md](08-ingress.md) | Truy cập từ ngoài: routing mesh + Traefik + HTTPS | Deploy Traefik, route nhiều domain/path |

---

## Học nhanh trong 1 buổi

Nếu chỉ có 2-3 tiếng:

1. Đọc **00-concepts** — nắm vocabulary
2. Làm **01-setup** (Option A — single node, 5 phút)
3. Làm **02-first-service** — cảm giác Swarm hoạt động thế nào
4. Đọc **04-stack** — hiểu `deploy:` block, quan trọng nhất cho thực tế
5. Đọc **05-secrets** — không dùng env var cho password

Các file còn lại đọc khi cần deploy thực tế.

---

## Concepts nhanh

```text
Node      = 1 máy trong cluster (Manager hoặc Worker)
Service   = định nghĩa "chạy image X với N bản"
Task      = 1 bản chạy cụ thể của service (1 container)
Stack     = nhóm service deploy từ compose.yaml
Overlay   = network ảo trải rộng nhiều host
```
