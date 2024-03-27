严格按照以下步骤操作：
环境 Ubuntu 20.04

1. 安装docker 

	```bash
	#下载docker 
	curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun 
	#添加当前用户到docker用户组 
	sudo usermod -aG docker $USER 
	newgrp docker 
	#配置docker镜像加速
	sudo mkdir -p /etc/docker

	sudo tee /etc/docker/daemon.json <<-'EOF'
	{
	  "registry-mirrors": ["https://punulfd2.mirror.aliyuncs.com"]
	}
	EOF

	#重启docker 
	sudo systemctl daemon-reload
	sudo systemctl restart docker
	```

2. 安装开发使用的go、node、jq

	```bash
	#下载二进制包
	wget https://golang.google.cn/dl/go1.19.linux-amd64.tar.gz
	#将下载的二进制包解压至 /usr/local目录
	sudo tar -C /usr/local -xzf go1.19.linux-amd64.tar.gz
	mkdir $HOME/go
	#将以下内容添加至环境变量 ~/.bashrc
	export GOPATH=$HOME/go
	export GOROOT=/usr/local/go
	export PATH=$GOROOT/bin:$PATH
	export PATH=$GOPATH/bin:$PATH
	#更新环境变量
	source  ~/.bashrc 
	#设置代理
	go env -w GO111MODULE=on
	go env -w GOPROXY=https://goproxy.cn,direct
	
	#下载nvm安装脚本
	wget https://gitee.com/real__cool/fabric_install/raw/main/nvminstall.sh
	#安装nvm；屏幕输出内容添加环境变量
	chmod +x nvminstall.sh
	./nvminstall.sh
	# 将环境变量写入.bashrc
	export NVM_DIR="$HOME/.nvm"
	[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
	[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
	# 更新环境变量
	source  ~/.bashrc
	# 安装node16
	nvm install 16
	#换源
	npm config set registry https://registry.npmmirror.com
	
	#安装jq 
	sudo apt install jq
	```



3. 克隆本项目 

	```bash
	git clone https://gitee.com/real__cool/fabric-trace
	```

4. 启动区块链部分。在fabric-trace/blockchain/network目录下:

	```bash
	# 仅在首次使用执行：下载Fabric Docker镜像
	./install-fabric.sh -f 2.5.6 d 
	```

	```bash
	# 启动区块链网络
	./start.sh
	# 关闭区块链网络
	./stop.sh
	```	
 	 **如果在启动区块链网络时遇到报错可以尝试:**
	```bash
	# 执行清理所有的容器指令：
	docker rm -f $(docker ps -aq)
	```
	**然后再重新启动区块链网络**

5. 启动后端 在fabric-trace/application/backend目录下： 执行： `go run main.go`

6. 修改后端IP，将以下文件中的IP：`127.0.0.1`，换成自己服务的IP。
	```bash
	fabric-trace/application/web/.env.development
	fabric-trace/application/web/src/router/index.js
	```

7. 新开一个窗口，启动前端 在fabric-trace/application/web目录下： 执行： 

	```bash
	# 仅在首次运行执行：安装依赖
	npm install 
	```

	```bash
	# 启动前端
	npm run dev
	```
