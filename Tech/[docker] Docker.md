# Docker

## Introduction

- Docker đóng gói các thành phần của ứng dụng vào một container (một môi trường độc lập và tách biệt với các container khác)

### Concepts

- **Volumes**: cho phép các container độc lập share chung folder, các file config hay static html

  ```sh
    docker volume create static-html-share-volume

    docker run -d --name node --mount source=static-html-share-volume,destination=/usr/share/html -p 2222:80 node

    docker run -d --name nginx2 --mount source=static-html-share-volume,destination=/usr/share/html -p 2223:80 node
  ```

- **Images**: là một khuôn mẫu để tạo container và sẽ được build dựa theo Dockerfile
- **Client**: tương tác với docker thông qua terminal, command sẽ đc gửi tới Docker Daemon
- **Docker Daemon**: lắng nghe yêu cầu từ Client
- **Docker Registry**: nơi lưu trữ docker images (AWS, Azure, Docker Hub)
- **Docker Repository**: tập hợp các docker images cùng tên nhưng khác tag
- **Docker Compose**: biên soạn và run nhiều docker containers

### Dockerfile

- **FROM**: image gốc (thường sử dụng tag alphine gọn nhẹ)
- **WORKDIR**: Thiết lập thư mục đang làm việc cho các cmd khác như: RUN, CMD, ENTRYPOINT, COPY, ADD,…
- **ARG**: chỉ dùng trong quá trình build images
- **ENV**: thiết lập biến môi trường key - value, cos
- **RUN**: chạy các lệnh khi build image (thường dùng để cài đặt các dependencies)
- **COPY**: sao chép các file, folder vào container
- **EXPOSE**: khai báo port lắng nghe của image.
- **VOLUME**: tạo một điểm gắn thư mục để truy cập và lưu trữ data.

## Commands

- List image/container:

```bash
  docker image/container ls
```

- Delete image/container:

```bash
  docker image/container rm <tên image/container >
```

- Delete all image hiện có:

```bash
  docker image rm $(docker images –a –q)
```

- List all container hiện có:

```bash
  docker ps [–a: all status]
```

- Start một container:

```bash
  docker start <container id>
```

- Stop a container:

```bash
  docker stop <container id>
```

- Stop all container:

```bash
  docker stop $(docker ps –a –q)
```

- Run container từ image và thay đổi tên container:

```cmd
  docker run
    [--env-file=.env]
    [–name <tên container>]
    <image id>
```

- Delete all container hiện có:

```bash
  docker rm $(docker ps –a –q)
```

- Show log a container:

```bash
  docker logs <container id>
```

- Build một image từ container:

```shell
  docker build
    [--build-arg arg_name=value] // set args for build image process
  -t <tên container>
```

- Tạo một container chạy ngầm:

```bash
  docker run -d <tên image>
```

- Tải một image trên docker hub:

```bash
  docker pull <tên image>
```
