## 1.Docker安装
      root@chen:/home/chen# sudo apt-get remove docker docker-engine docker.io containerd runc
---
      sudo apt-get install \
      apt-transport-https \
      ca-certificates \
      curl \
      gnupg-agent \
      software-properties-common
---
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
---
      sudo apt-key fingerprint 0EBFCD88
---
      sudo add-apt-repository \
      "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
       $(lsb_release -cs) \
       stable"
---
      sudo apt-get install docker-ce docker-ce-cli containerd.io(需要能上外网,不然很慢)
      
      
## 2.kubectl安装（官网安装）
      中文文档：https://k8smeetup.github.io/docs/tasks/tools/install-kubectl/
      英文文档：https://kubernetes.io/docs/tasks/tools/install-kubectl/
      
      
## 3.helm安装
       注意：不用命令行安装Helm -> 可能会失误操作,将阿里云服务端tiller版本更新掉
      
      a.官方下载指定版本,对应阿里云自己配置的服务端版本（helm-v2.14.3-linux-amd64.tar.gz）
       https://github.com/helm/helm/releases
       wget https://get.helm.sh/helm-v2.14.3-linux-amd64.tar.gz
      b.将文件夹解压,将可执行文件提取到/usr/local/bin/
      c.配置（先连接阿里云,后面再执行 step_5）
          sudo helm init
      d.安装确认（先连接阿里云,后面再执行 step_5）
          chen@chen:~$ su root
          密码： 
          root@chen:/home/chen# helm version
          Client: &version.Version{SemVer:"v2.14.3", GitCommit:"0e7f3b6637f7af8fcfddb3d2941fcc7cbebb0085", GitTreeState:"clean"}
          Server: &version.Version{SemVer:"v2.14.3", GitCommit:"0e7f3b6637f7af8fcfddb3d2941fcc7cbebb0085", GitTreeState:"clean"}
          root@chen:/home/chen# 
      
## 4.连接阿里云集群配置
      root@chen:~# cd .kube/
      bash: cd: .kube/: 没有那个文件或目录
      root@chen:~# mkdir /root/.kube
      root@chen:~# cd .kube/
      root@chen:~/.kube# 
      https://signin.aliyun.com/soundbus-rd.onaliyun.com/login.htm
      
      root@chen:~/.kube# sudo gedit config（加入阿里云k8s管理端config信息:https://help.aliyun.com/document_detail/86494.html）

      ** (gedit:3251): WARNING **: 21:10:38.001: Set document metadata failed: 不支持设置属性 metadata::gedit-spell-language

      ** (gedit:3251): WARNING **: 21:10:38.010: Set document metadata failed: 不支持设置属性 metadata::gedit-encoding

      ** (gedit:3251): WARNING **: 21:10:39.707: Set document metadata failed: 不支持设置属性 metadata::gedit-position
      
      root@chen:~/.kube# kubectl get ns
      NAME                          STATUS   AGE
      ambassador                    Active   23d
      argo                          Active   3d5h
      argocd                        Active   34h
      arms-pilot                    Active   32d

## 5.部署、更新项目到阿里云集群配置（采用Helm方式的操作）
      root@chen:~/.kube# kubectl get ns
      NAME                          STATUS   AGE
      ambassador                    Active   23d
      argo                          Active   3d5h
      argocd                        Active   34h
      arms-pilot                    Active   32d
      tools                         Active   36d
      root@chen:~/.kube# sudo helm init
      Creating /root/.helm 
      Creating /root/.helm/repository 
      Creating /root/.helm/repository/cache 
      Creating /root/.helm/repository/local 
      Creating /root/.helm/plugins 
      Creating /root/.helm/starters 
      Creating /root/.helm/cache/archive 
      Creating /root/.helm/repository/repositories.yaml 
      Adding stable repo with URL: https://kubernetes-charts.storage.googleapis.com 
      Adding local repo with URL: http://127.0.0.1:8879/charts 
      $HELM_HOME has been configured at /root/.helm.
      Warning: Tiller is already installed in the cluster.
      (Use --client-only to suppress this message, or --upgrade to upgrade Tiller to the current version.)
      root@chen:~/.kube# helm version
      Client: &version.Version{SemVer:"v2.14.3", GitCommit:"0e7f3b6637f7af8fcfddb3d2941fcc7cbebb0085", GitTreeState:"clean"}
      Server: &version.Version{SemVer:"v2.14.3", GitCommit:"0e7f3b6637f7af8fcfddb3d2941fcc7cbebb0085", GitTreeState:"clean"}
      root@chen:~/.kube# 

## 6.项目配置（自己写脚本部署、更新服务）
      root@chen:~/ideaProject/schedule-pool/push-schedule-service$ cd schedule-server-manage/
      1.1 打包镜像、上传镜像库（执行时，输入版本参数）
      root@chen:~/ideaProject/schedule-pool/push-schedule-service/schedule-server-manage$ ./buildimage.sh 1.0.10
      root@chen:~/ideaProject/schedule-pool/push-schedule-service/schedule-server-manage$ cd charts/
      1.2 启动服务
      root@chen:~/ideaProject/schedule-pool/push-schedule-service/schedule-server-manage/charts$ ./helm-install.sh

      root@chen:~/ideaProject/schedule-pool/push-schedule-service$ cd schedule-server-manage/
      2.1 打包镜像、上传镜像库（执行时，输入版本参数）
      root@chen:~/ideaProject/schedule-pool/push-schedule-service/schedule-server-manage$ ./buildimage.sh 1.0.11
      root@chen:~/ideaProject/schedule-pool/push-schedule-service/schedule-server-manage$ cd charts/
      2.2 更新服务
      root@chen:~/ideaProject/schedule-pool/push-schedule-service/schedule-server-manage/charts$ ./helm-upgrade.sh

