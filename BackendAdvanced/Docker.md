# 1. Docker basics
## 1.1 Benefits from containers
- **Works on my machine problem**: mỗi người cấu hình 1 cách khác nhau để phát triển --> khi deploy lên môi trường ko work
- **Isolated environments**: mooi trường deploy có các ứng dụng Python sẵn có với version cũ 2.6, Bạn cần deploy sẻvice với Python 2.7
	- Sẽ không có gì nếu chia thành các node riêng và deploy isolated environment
- **Development**: khi bạn deploy sản phảm lên, cần cài đặt toàn bộ dependencies. Headache để start installing và managing the development database on your own.
- Scaling: start và stop container sẽ dễ dàng hơn. Nhưng sẽ còn nheieuf challenge khác như load balancing và container orchestration
## 1.2 Virtual machine
![[Pasted image 20260604102602.png]]
Container share chung tài nguyên OS và package application và các dependencies, lightweight and efficient solution.
Container cung cấp faster startup, sử dụng hiệu quả tài nguyên, và khả năng linh động khi thay đổi môi trường. (mang docker image đi là xong)
## 1.3 Container and Image
Cần image và container runtime (docker engine) để tọa ra container.
Image cung cấp tất cả lệnh và dependencies cần thiết cho container hoạt động.
## 1.3.1 Image
Image được build từ instructional file named Dockerfile được parsed khi bạn chạy lệnh *docker image build*
``` dockerfile
FROM <image>:<tag>

RUN <install some dependencies>

CMD <command that is executed on `docker container run`>
```
Dockerfile cung cấp các instructions để build image, do chúng ta tự viết ra.
### 1.3.2 Containers

## 1.4 Docker CLI
- docker run -d nginx: detached mode, run trong background
- docker exec -it ubuntu bash: -i (interactive) and -t (tty) quản lý process trong ubuntu(các chương trình) --> lệnh dùng để running process inside container
- docker run -d --name logger devopsdockeruh/simple-web-service:ubuntu: tạo 1 container có name = logger từ image devopsdockeruh/simple-web-service:ubuntu

## 1.5 Running and stopping containers
docker run ubuntu: cơ bản nhất để tạo và chạy 1 container
docker run -t ubuntu: tạo ra 1 tty để thao tác trên bên trong container

Command ubuntu:
apt-get update
apt-get -y install curl
# 2. In-depth dive into images
## 2.1 Where do the images come from?
docker search: tìm kiếm trong Docker Hub
docker pull: kéo image từ Docker hub về
## 2.2 A detailed look into an image
docker pull ubuntu:latest lấy bản mới nhất
Tag là 1 cách để rename image
*registry/organisation/image: tag*: 3 thành phần của image name
## 2.3 Building images
Dockerfile là file chứa các build instructions cho image. Định nghĩa các dependencies include vào image 
Alpine: small Linux distribution được sử dụng để tạo small images
``` dockerfile
# Start from the alpine image that is smaller but no fancy tools
FROM alpine:3.21

# Use /usr/src/app as our workdir. The following instructions will be executed in this location.
WORKDIR /usr/src/app

# Copy the hello.sh file from this directory to /usr/src/app/ creating /usr/src/app/hello.sh
COPY hello.sh .

# Alternatively, if we skipped chmod earlier, we can add execution permissions during the build.
# RUN chmod +x hello.sh

# When running docker run the command will be ./hello.sh
CMD ["./hello.sh"]
```

Lệnh build image
docker build -t hello-docker: build image với tên hello-docker
Trong quá trình build có 3 steps tương ứng với 3 layers của image bên trên **base image** (alpine:3.21)
Nếu thay đổi Dockerfile ở last lines thì sẽ start từ previous layer và skip thẳng tới section thay đổi. Lệnh COPY cũng auto detech changes in the files, chạy từ step 3/3.
Giúp **tối ưu build pipelines** --> optimization in chapter 4

- docker diff : kiểm tra thay đổi
	- A: added
	- D: deleted
	- C: changed
- docker commit: lưu các thay đổi (thêm 1 layer lên trên image) --> tạo ra 1 image mới (v2 tag)
Không sử dụng docker commit nữa mà sẽ tạo ra image mới với Dockerfile thay đổi thêm command RUN trong Dockerfile (RUN touch additional.txt)
```
docker build -t hello-docker:v2 .
```

Tham số cuối cùng trong `docker run` sử dụng để đưa ra command hoặc là arguement (thường là sẽ có service trong container có hỗ trợ truyền tham số không)
Thử câu `docker run devopsdockeruh/simple-web-service:alpine hello` sẽ đọc tham số 'hello' nhưng sẽ thông báo rằng 'hello' không phải là 1 arguement
## 2.4 Defining start conditions for the container
```Dockerfile
FROM ubuntu:24.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python3 ffmpeg
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
RUN chmod a+x /usr/local/bin/yt-dlp

CMD ["/usr/local/bin/yt-dlp"]
```
Cách viết Dockerfile cũ:
- CMD: trỏ lệnh tới file chạy chương trình
- WORKDIR: thư mục làm việc

`docker run yt-dlp https://www.youtube.com/watch?v=uTZSILGTskA`: gặp lỗi khi truyền command, tham số argument (tham số môi trường)
- `docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "https://www.youtube.com/watch?v=uTZSILGTskA": stat https://www.youtube.com/watch?v=uTZSILGTskA: no such file or directory: unknown. ERRO[0000] error waiting for container: context canceled`
Truyền như này sẽ thay thế command (CMD)

Cách làm đúng để append vào command --> sử dụng ENTRYPOINT để xác định main executable, từ đó Docker sẽ append arguements vào đó.

Update:
``` Dockerfile
FROM ubuntu:24.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python3 ffmpeg
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
RUN chmod a+x /usr/local/bin/yt-dlp

# Replacing CMD with ENTRYPOINT
ENTRYPOINT ["/usr/local/bin/yt-dlp"]
```

Có thể truyền tham số vào command rồi: `docker run yt-dlp https://www.youtube.com/watch?v=XsqlHHTGQrw`

Đôi lúc cách tạo image với ENTRYPOINT và CMD có thể gây confusing
--> khi mà define cả ENTRYPOINT và CMD thì có tác dụng khác:
	- CMD: đưa ra default arguments vào entry point
``` Dockerfile
FROM ubuntu:24.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python3 ffmpeg
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
RUN chmod a+x /usr/local/bin/yt-dlp

ENTRYPOINT ["/usr/local/bin/yt-dlp"]

# define a default argument
CMD ["https://www.youtube.com/watch?v=Aa55RKWZxxI"]
```

Khi ta chạy lệnh không truyền argument `docker run yt-dlp` nó sẽ tự hiểu là tham số mặc định ở CMD

Có 2 cách set ENTRYPOINT và CMD: **exec** form và **shell** form. Cách trên làm là exec form command sẽ tự chạy
Shell form: command sẽ wrapped với `/bin/sh -c`, tuy nhiên sẽ có lợi trong 1 số tình huống
VD: evaluate environment variables trong command như `$MYSQL_PASSWORD` hoặc tương tự vậy
Shell form: command cung cấp dưới dạng string mà không có brackets. Trong `exec form` command và arguments được cung cấp dạng list (with brackets)
![[Pasted image 20260605151010.png]]

Trong hầu hết trường hợp, bỏ qua ENTRYPOINT mà chỉ cần sử dụng CMD.
VD: Ubuntu image mặc định ENTRYPOINT tới sh. Và cho phép dễ dàng overwrite the CMD easily (VD: bash để inside container)

VD: 
``` cmd
$ docker run -it python:3.11 Python 3.11.8 (main, Feb 13 2025, 09:03:56) [GCC 12.2.0] on linux Type "help", "copyright", "credits" or "license" for more information. 
>>> print("Hello, World!") 
>>> Hello, World! >>> 
>>> exit()
$ docker run -it python:3.11 --version docker: Error response from daemon: OCI runtime create failed: container_linux.go:370: starting container process caused: exec: "--version": executable file not found in $PATH: unknown. 
$ docker run -it python:3.11 bash 
root@1b7b99ae2f40:/#
```
Trong VD thấy ENTRYPOINT trỏ tới gì đó khác với python, nhưng CMD là python và ta có thể overwrite it.  (Lệnh --version không thể chạy được )
## 2.5 Interacting with the container via volumes and ports
## 2.5.1 Volumes
Là nơi lưu trữ data trong containers, tạo và quản lý bởi Docker. Bạn có thể tạo volume explicitly sử dung `docker volume create` command hoặc Docker tự tạo khi container hoặc service create
Folder volumes ở trên máy chủ (host) container đó, nếu muốn có thể mount vào container
### 2.5.2 Bind mounts
Khi sử dụng, file hoặc directory ở host machine được mounted từ host vào container. Ngược lại, sử dụng volume thì new directory được tạo trong Docker's storage directory trong host machine, nhưng chỉ container được access trực tiếp đến nó sử dụng standard filesystem operations

`docker run -v "$(pwd):/mydir" yt-dlp https://www.youtube.com/watch?v=saEpkcVi1d4`: binding vào thư mục mydir trong máy host

VD: `docker run -v "$(pwd)/text.log:/usr/src/app/text.log" devopsdockeruh/simple-web-service`
$(pwd)/text.log: là đường dẫn tới file trong máy local
/usr/src/app/text.log: đường dẫn file trong container

## 2.6 Allowing external connections into containers
Cho phép map port từ host machine tới container port
Để expose port, từ khóa `EXPOSE <port>` trong Dockerfile
Để publish port, chạy container với `-p <host-port>:<container-port>`

## 2.7 Utilizing tools from the Registry


# 3. Docker compose
- Container orchestration with Docker compose and relevant concepts such as `docker network`
Mục tiêu đạt được:
- Run a group of containerized application that interact with each other via HTTP
- Run a group of containerized application that interact with each other via volumes
- Manually scale application
- Sử dụng 3rd party services, như là db, bên trong containers
## 3.1 Migrate to docker compose
Cho phép chạy multi-container applications sử dụng single command `docker compose -f <arg> `

Giả sử đang có file Dockerfile như sau:
``` dockerfile
FROM ubuntu:24.04

WORKDIR /mydir

RUN apt-get update && apt-get install -y curl python3 ffmpeg
RUN curl -L https://github.com/yt-dlp/yt-dlp/releases/latest/download/yt-dlp -o /usr/local/bin/yt-dlp
RUN chmod a+x /usr/local/bin/yt-dlp

ENTRYPOINT ["/usr/local/bin/yt-dlp"]
```
Giờ tạo file `docker-compose.yaml`
``` docker-compose
services:
	yt-dlp-ubuntu:
		image: <username>/<repositoryname>
		build: .
```
Khai báo 1 service có tên là `yt-dlp-ubuntu`
Giá trị key `build` có thể là file system path (ví dụ đang trỏ đến folder hiện tại)

- docker compose build: build image
- docker compose push: push image in docker hub
### 3.1.2 Volume in Docker compose
Volume trong docker compose được tính với syntax sau: `location-in-host: location-in-container`
``` docker-compose
services:
  yt-dlp-ubuntu:
    image: <username>/<repositoryname>
    build: .
    volumes:
      - .:/mydir
    container_name: yt-dlp
```
Trong ví dụ trên volume đang trỏ tới folder hiện tại trong local host
`$ docker compose run yt-dlp-ubuntu https://www.youtube.com/watch?v=saEpkcVi1d4`
Lệnh trên để chạy chương trình (build + run)
### 3.1.3 Using a ready image with Docker Compose
Việc sử dụng ready images là phổ biến
```docker-compose
services:
  nginx:
    image: nginx:1.27
  database:
    image: postgres:17
```
Sau đó chạy lệnh `docker compose up` để active toàn bộ service
### 3.1.4 Key commands in Docker compose
- docker compose up: start all services defined in docker-compose.yaml
- docker compose down: remove all running services
- docker compose logs: monitor the output of running containers
- docker compose ps: list all services
