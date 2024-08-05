# 我的图标库 - My Icons


# 目录

[一、简介](#一简介)

[二、改动](#二改动)

[三、展示](#三展示)

[四、使用](#四使用)

[五、分类](#五分类)

[六、本地运行](#六本地运行)

[七、Docker部署](#七Docker部署)

# 一、简介

基于[Siriling/my-icons](https://github.com/Siriling/my-icons)&[heizicao/My Icon](https://gitee.com/heizicao/my-icon)修改，提供在线图标链接，用于个人NAS设备显示使用，禁止用于商业用途

# 二、改动

* 删除原来的CDN开关，改为选择框选择
* 网页展示可选从CDN读取或者是本地读取
* 除了原来的服务器直链下载，还增加了jsDelivr、123云盘直链、GitHubProxy直链，拥有更多链接选择，也可以自行添加修改更多选择
* 新增图标分类：群晖
* 优化搜索框和搜索按钮显示
* 抛弃原来的else if判断，改为数组判断
* 当选择了123云盘时，自动鉴权并生成直链URL
* 当未选择来源时，提示选择来源后重试
* 当浏览器权限未开启时，弹窗提示开启网站权限
* 把原来的代码做了一些小优化

# 三、展示

![效果展示](https://mirror.ghproxy.com/https://raw.githubusercontent.com/ui-beam-9/my-icons/master/src/assets/image-1.png)

![提示选择来源后重试](https://mirror.ghproxy.com/https://raw.githubusercontent.com/ui-beam-9/my-icons/master/src/assets/image-2.png)

![弹窗提示开启网站权限](https://mirror.ghproxy.com/https://raw.githubusercontent.com/ui-beam-9/my-icons/master/src/assets/image-3.png)

![多个来源选择](https://mirror.ghproxy.com/https://raw.githubusercontent.com/ui-beam-9/my-icons/master/src/assets/image-4.png)

![123云盘直链输出](https://mirror.ghproxy.com/https://raw.githubusercontent.com/ui-beam-9/my-icons/master/src/assets/image-5.png)
# 四、使用

- 点击相应图标即可获取URL

- 点击相应图标即可查看部署教程

- 打开CDN开关，可转换为CDN链接

# 五、分类
- Docker容器（docker）
- 路由器（router）
- 虚拟机（vms）
- 群晖（synology）
- 项目（project）
- 其他（other）

# 六、本地运行

下载安装node.js，在终端运行以下两个命令，然后在浏览器打开http://localhost:3005预览测试

```shell
npm install
npm run serve
```
待测试没问题之后，运行以下命令进行编译
```shell
npm run build 
```
编译后，会看到新增一个叫做dist的文件夹，里面就是编译后的文件, 直接将dist文件夹放到Nginx的www文件夹中即可
# 七、Docker部署
1. Docker部署Nginx服务
2. 下载本仓库
3. 进入my-icons文件夹
4. 运行编译命令
```shell
npm run build
```
5. 将dist文件夹放到Nginx的www文件夹中
6. 运行Nginx服务
```shell
docker run -d --name nginx -p 80:80 -v /www/my-icons/dist:/usr/share/nginx/html nginx
```
7. 打开浏览器访问http://localhost:3005