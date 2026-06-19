# Swarm — Setup Cluster để Thực Hành

> Mục tiêu: có cluster Swarm chạy được để thực hành các bài tiếp theo.

---

## Lựa chọn môi trường

| Option | Phù hợp khi | Khó khăn |
|--------|------------|----------|
| **A. Single-node** (1 máy) | Học concepts nhanh, không cần setup nhiều | Không mô phỏng được multi-host thực tế |
| **B. Multi-node với Docker-in-Docker** | Mô phỏng cluster thực trên 1 máy | Cần RAM ~4GB |
| **C. Play with Docker** (online) | Không có máy mạnh, muốn thử nhanh | Cần internet, session tồn tại 4 giờ |

---

## Option A — Single Node (Khuyến nghị cho người mới)

Chạy trên máy local, đủ để học hầu hết concepts.

```bash
# Khởi tạo Swarm — máy hiện tại trở thành Manager
docker swarm init

# Kết quả sẽ hiện ra lệnh join cho worker (lưu lại để dùng sau):
# Swarm initialized: current node (xxx) is now a manager.
# To add a worker to this swarm, run the following command:
#   docker swarm join --token SWMTKN-xxx 192.168.x.x:2377
```

```bash
# Kiểm tra cluster
docker node ls
# ID                HOSTNAME   STATUS    AVAILABILITY  MANAGER STATUS
# abc123 *          myhost     Ready     Active        Leader
```

> Dấu `*` = node bạn đang đứng. `Leader` = manager đang điều phối cluster.

**Hạn chế của single-node:** Task luôn chạy trên 1 node duy nhất, không thấy được failover thực tế. Nhưng đủ để học commands và YAML.

---

## Option B — Multi-node với Docker-in-Docker

Tạo 3 "máy ảo" là Docker container chạy Docker bên trong — gọi là **DinD (Docker in Docker)**.

### Bước 1: Tạo network riêng

```bash
docker network create --driver bridge swarm-net
```

### Bước 2: Khởi động 3 node (1 manager + 2 worker)

```bash
# Manager node
docker run -d \
  --name manager \
  --hostname manager \
  --network swarm-net \
  --privileged \
  -p 8080:80 \
  docker:dind

# Worker 1
docker run -d \
  --name worker1 \
  --hostname worker1 \
  --network swarm-net \
  --privileged \
  docker:dind

# Worker 2
docker run -d \
  --name worker2 \
  --hostname worker2 \
  --network swarm-net \
  --privileged \
  docker:dind
```

### Bước 3: Init Swarm trên manager

```bash
# Chạy lệnh bên trong container manager
docker exec manager docker swarm init --advertise-addr manager
```

Kết quả cho ra lệnh join, dạng:
```
docker swarm join --token SWMTKN-xxx manager:2377
```

### Bước 4: Join worker vào cluster

```bash
# Lấy token (nếu lỡ mất lệnh join)
docker exec manager docker swarm join-token worker

# Join từng worker (thay token bằng token thực)
docker exec worker1 docker swarm join --token SWMTKN-xxx manager:2377
docker exec worker2 docker swarm join --token SWMTKN-xxx manager:2377
```

### Bước 5: Kiểm tra

```bash
docker exec manager docker node ls
# ID          HOSTNAME   STATUS   AVAILABILITY  MANAGER STATUS
# abc * Ready  manager    Ready    Active        Leader
# def   Ready  worker1    Ready    Active
# ghi   Ready  worker2    Ready    Active
```

### Helper script — tạo và xóa cluster nhanh

```bash
# Lưu thành file create-cluster.sh
#!/bin/bash
docker network create swarm-net 2>/dev/null || true

for node in manager worker1 worker2; do
  docker run -d --name $node --hostname $node \
    --network swarm-net --privileged docker:dind
done

sleep 3  # chờ daemon khởi động

docker exec manager docker swarm init --advertise-addr manager

TOKEN=$(docker exec manager docker swarm join-token worker -q)
docker exec worker1 docker swarm join --token $TOKEN manager:2377
docker exec worker2 docker swarm join --token $TOKEN manager:2377

echo "✅ Cluster ready"
docker exec manager docker node ls
```

```bash
# Lưu thành file destroy-cluster.sh
#!/bin/bash
docker rm -f manager worker1 worker2
docker network rm swarm-net
echo "✅ Cluster destroyed"
```

```bash
chmod +x create-cluster.sh destroy-cluster.sh
./create-cluster.sh
```

---

## Option C — Play with Docker (Online)

1. Vào [labs.play-with-docker.com](https://labs.play-with-docker.com)
2. Đăng nhập bằng Docker Hub account
3. Click **+ ADD NEW INSTANCE** để thêm node
4. Thêm 3 instance (node1, node2, node3)
5. Trên node1: `docker swarm init --advertise-addr <IP hiện ra trên màn hình>`
6. Copy lệnh join, chạy trên node2 và node3

> Session tồn tại **4 giờ**, đủ để thực hành xong 1 buổi.

---

## Alias tiện dụng khi học

Nếu dùng Option B, gõ `docker exec manager` mãi sẽ mệt. Thêm alias:

```bash
# Thêm vào ~/.bashrc hoặc ~/.zshrc
alias dm='docker exec manager docker'
alias dw1='docker exec worker1 docker'
alias dw2='docker exec worker2 docker'

# Dùng như sau:
dm node ls
dm service ls
dm stack deploy -c compose.yaml myapp
```

---

## Kiểm tra cluster hoạt động tốt

```bash
# Xem tất cả node
docker node ls

# Xem thông tin chi tiết 1 node
docker node inspect manager --pretty

# Kết quả mong đợi: tất cả node STATUS = Ready, AVAILABILITY = Active
```

---

## Dọn dẹp

```bash
# Rời khỏi Swarm (trên single-node)
docker swarm leave --force

# Nếu dùng DinD
./destroy-cluster.sh
```

---

**Tiếp theo:** [02-first-service.md](02-first-service.md) — Deploy service đầu tiên lên cluster.
