# springboot-demo-oraclejdk8u131

## 克隆项目

```bash
mkdir ~/workspace
cd ~/workspace
git clone https://gitee.com/baihr/springboot-demo-java8u131.git
```

## 部署 OracleJDK 8u131

访问 Oracle 官方网站 [Java Archive Downloads - Java SE 8](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html) 下载所需的 JDK 版本，或者从网盘下载：https://pan.baidu.com/s/1DO1MDJepil58HqD1z9YRyw?pwd=fej3，执行以下步骤安装：

```bash
sudo mkdir /usr/java
sudo tar -xzvf jdk-8u131-linux-x64.tar.gz -C /usr/java
```

编辑`/etc/profile`，设置环境变量：

```bash
export JAVA_HOME=/usr/java/jdk1.8.0_131
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

执行`source /etc/profile`，使 profile 文件生效后，执行`java -version`验证。

## 部署Mavne

```bash
cd /tmp
wget https://dlcdn.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz
sudo tar -zxvf apache-maven-3.9.8-bin.tar.gz -C /opt

# 设置 PATH
echo -e '# set maven\nPATH="/opt/apache-maven-3.9.8/bin:$PATH"\n' >> ~/.bashrc
source ~/.bashrc

# 验证
mvn -v
```

编辑配置文件`vim /opt/apache-maven-3.9.8/conf/settings.xml`，在`<mirrors>`标签下添加以下镜像源：

```xml
    <mirror>
      <id>aliyunmaven</id>
      <mirrorOf>*</mirrorOf>
      <name>aliyun public repository</name>
      <url>https://maven.aliyun.com/repository/public</url>
    </mirror>
```

## 构建应用

```bash
cd ~/workspace/springboot-demo-java8u131
mvn clean
mvn package
```

## 启动应用

注意，以下命令加载并启用了 APM 探针。

```bash
nohup java \
  -javaagent:$HOME/workspace/probe/dd-java-agent.jar \
  -Ddd.service.name=springboot-demo-oraclejdk8u131 \
  -Ddd.env=dev \
  -Ddd.version=0.0.1 \
  -Ddd.profiling.enabled=true \
  -Ddd.profiling.ddprof.enabled=true \
  -jar target/springboot-demo-oraclejdk8u131-0.0.1-SNAPSHOT.jar > output.log 2>&1 &
```

## 测试接口

- http://127.0.0.1:8080/hello?name=lisi ，此接口加入了 1 秒 Sleep
- http://127.0.0.1:8080/user
- http://127.0.0.1:8080/save_user?name=newName&age=18 ，此接口加入除 0 异常
- http://127.0.0.1:8080/html
- http://127.0.0.1:8080/user/123/roles/456
- http://127.0.0.1:8080/javabeat/somewords

接口逻辑见代码`springboot-demo-java8u131/src/main/java/com/example/springbootdemojava8u131/demos/web`路径下的文件。

