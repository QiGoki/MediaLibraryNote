 

# TrueNas应用安装手册

## 零、TrueNas-Scale安装指南

### 1.安装系统

> 官方下载网址：[Download TrueNAS SCALE - Data Storage Software](https://www.truenas.com/download-truenas-scale/)

1. 右下角输入邮箱或No Thakn You。

   ![image-20240815161354174](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815161354174.png)

2. 下载好镜像之后安装即可。

### 2.控制台初始化

1. 安装完成后终端会见到以下画面，则安装完毕。

   ![image-20240815162203443](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815162203443.png)

2. 这里会有网络设置和密码设置，也可以接入控制台Shell。

   强烈建议先通过软路由或路由器设置端将TrueNas绑定为固定IP，方便后面操作。

### 3.基本设置

1. WebUI：

   ![image-20240815162740860](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815162740860.png)

2. 同样先设置中文，System Settings->General->Localization Setttings，按你的喜好设置完成。

   ![image-20240815162858994](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815162858994.png)

3. 同样还是这个页面，可以设置WebUI默认端口，在这个设置里面。

   ![image-20240815163117975](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815163117975.png)

### 4.设置池

1. 依次点击存储->创建池

2. 在第二项数据布局中，选择想要的布局，具体区别建议请咨询二代。![image-20240815163415494](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815163415494.png)

3. 设置好池之后，就可以管理你的数据集啦。这里可以提前按照[一、前期准备->1.新建应用管理文件夹](#1.新建应用管理文件夹)设置好文件夹目录和新建用户，方便下一步共享。


### 5.共享池

> 池是不能从根目录开始共享的，所以需要提前新建文件夹。
>
> 最新版本的TrueNas提供了三种共享方式，分别是SMB、NFS、块共享，之前的WebDav更新到了应用的官方仓库中，有需要的可以自行安装。

1. **设置SMB共享，依次设置共享目录及共享名称。**

   ![image-20240815164728374](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815164728374.png)

   链接共享时需要的登录用户即为被分享文件夹的所有者。

   当然为了安全保证也可以设置访客登录，非所有者登陆时是只有读权限的。

   ![image-20240815164836859](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815164836859.png)

2. **Windows添加共享。**

   我的电脑->右键空白处->添加一个网络位置 

   或 我的电脑->![image-20240815165024816](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815165024816.png)这个三个点->映射网络驱动器

   按照自己的习惯来都可以。

   ![image-20240815165231810](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815165231810.png)

   这里的**\Share**为你之前共享时设置的共享名，可以只输到IP结束，然后点击浏览选择要映射的文件夹。

3. **Linux添加共享**

   这是之前自己给CentOS写的笔记同样适用于rockyLinux，其他系统请自行搜索。

   > ## 挂载SMB
   >
   > ### 1.给Centos系统安装cifs-utils软件包
   >
   > ```sh
   > sudo yum install cifs-utils
   > ```
   >
   > ### 2.新建一个目录
   >
   > ```sh
   > sudo mkdir /mnt/samba
   > ```
   >
   > ### 3.使用 mount 命令挂载Samba共享
   >
   > ```sh
   > sudo mount -t cifs //<Samba服务器IP>/<共享名称> /mnt/samba -o username=<Samba用户名>,password=<Samba密码>,uid=<本地用户名>,gid=<本地用户组名>
   > 
   > # /etc/fstab中的写法：
   > //<ip>/Share /Share cifs defaults,username=,password= 0 0
   > rockyLinux中，下列命令刷新：
   > systemctl daemon-reload
   > ```
   >
   > 查看root用户的uid和gid的命令
   >
   > ```sh
   > id root
   > ```
   >
   > ### 4.卸载Samba共享
   >
   > ```sh
   > sudo umount /mnt/myshare
   > ```
   >

## 一、前期准备

### 1.新建应用管理文件夹

> 鉴于各应用的设置文件和数据文件都有着不错的集成和整理，以及TrueNAS操蛋的权限管理，这里推荐专门新建一个文件夹用于放置各个应用的config.

> 注：如果不单独设置的话，后面自定义的时候TrueNAS会自动新建一个ix-applications文件夹管理应用。但是这个文件夹是不能共享出来的，要管理只能通过命令行或者ssh进系统底层管理。

![image-1](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240806163425029.png)

- 数据集->想要创建的数据集（选好相应父目录）->添加数据集->输入名称（必须英文）->保存

![image-2](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240806163913819.png)

![image-3](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240806164224389.png)

> 注1：想要中文的话可以去设置好共享之后在win下新建文件夹，但是不推荐，因为不方便管理文件夹权限，同时TrueNas这边看不到你在其他地方新建的文件夹，一些内建操作不能使用。通过共享链接新建的一切东西的权限都属于链接共享时登录的账户。
>

### 2.设置权限

> 鉴于TrueNAS操蛋的权限设置（再次强调），后面有很多重复的工作，这一步尽量做好学会。

1. **新建一个用户用于管理（如果设置共享的时候已经设置了可以省略）**

   - 依次点击用户凭证->本地用户->添加![image-20240806165757951](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240806165757951.png)

   - ​	填写用户名、密码、UID，其余的不用动。<img src="https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240806165922226.png" alt="image-20240806165922226" style="zoom:80%;" />
   - 点击保存，这样一个最简单的新用户就创建好了。
   - 有需要的可以把命令行、允许sudo等点上。

2. **选择上一步新建的文件夹，在右侧权限项点击编辑。**![image-20240806165217018](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240806165217018.png)

3. **选择刚刚新建的用户和用户组，并记得点击应用于用户和应用与组**

   ![image-20240811142311165](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811142311165.png)
   
   > 注：经实践发现，在父文件夹的该界面下，点击**递归应用权限****及弹出的**将权限应用于子数据集**也可以直接设置权限，但也不是一劳永逸。

### 3.设置池

- **依次点击应用->设置->选择池，选择刚刚新建的管理文件夹所在的数据集。**![image-20240806172300781](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240806172300781.png)

## 二、目录层级管理

> 经过实践研究，结合emby、qbittorrent、moviepilot这三个关键应用的使用。这里推荐的目录层级如下：
>
> -- main（主池）
>
> ​	-- ApplicationData（各应用config存放文件夹）
>
> ​		--emby_config
>
> ​		--qbittorrent_config
>
> ​		--...
>
> ​	-- Media（影音库主文件夹）
>
> ​		-- movie（电影）
>
> ​		-- tv（电视剧）
>
> ​		-- downloads（qb影音下载临时文件夹）.

**解释：**

1. **管理与存储分开存放，一是便于管理；二是用户目录干净。**

2. **如果有别的需求，例如qb下载非影音库文件或需要做种，可以按如下设置目录**

   - > -- main（主池）
     >
     > ​	-- ApplicationData（各应用config存放文件夹）
     >
     > ​		--emby_config
     >
     > ​		--qbittorrent_config
     >
     > ​		--...
     >
     > ​	-- Media（影音库主文件夹及下载主文件夹）
     >
     > \#truenas的池有点奇怪，只有在子目录下才能硬链接。
     >
     > ​		-- movie（电影）
     >
     > ​		-- tv（电视剧）
     >
     > ​	--otherDownloads（存放qb下载的其他类型文件）

- **新建好层级目录之后，选择media目录，点击右侧的权限编辑**

  ![image-20240811154919989](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811154919989.png)

- **点击递归应用权限，即可设置好子目录的所有权限。**

  ![image-20240811155005371](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811155005371.png)

## 三、Emby安装

> 注：TrueNas所有应用的安装逻辑实际上都是跟docker一样的，因此碰到问题的时候可以先去dockerHub相关页面去找解决方法。

> [EmbyDockerHub安装指南](https://hub.docker.com/r/emby/embyserver)
>
> 注：这里只展示必要设置，其他扩展设置请参考EmbyDockerHub。

### 1.TrueNas安装应用

1. Emby是一个影音库管理应用，用于影音展示、播放、全屋串流等功能。

   - pc端：![image-20240811145812955](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811145812955.png)

2. Emby现已集成在TrueNas官方源中，可以直接安装。

3. 依次点击**应用->探索应用程序->找到Emby点击安装**（没找到的话注意要科学上网）

   ![image-20240811150309068](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811150309068.png)

4. **该界面的用户和群组ID为刚刚新建的用户ID**，**WebPort为转发出来的WebUI的端口号**

   ![image-20240811153128007](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811153128007.png)

5. **这里是目录映射设置。**

   ![image-20240811160249959](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811160249959.png)

   - **所有通过官方镜像安装的应用都会预设好程序需要的目录名称，如上方第一个红框中的Config Storage，即对应docker中-p /path/to/config:/config。**

   - **Additional Storage则是自定义映射的目录。点击添加可以映射多个目录。**

   - **需要注意的是这里的目录类型：**

     - Host Path指映射系统中存在的目录，即自定义目录。
     - ixVolume指由TrueNas自动创建，不推荐，因为不能通过分享管理，只能通过命令行。
     - SMB Share指外挂一个SMB共享来映射目录，有需要可以使用。

     ![image-20240811153656341](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240811153656341.png)

6. **其他项不用额外设置，点击安装即可。**

7. **注：右侧“历史”为部署应用时的log，可供参考。**

   ​		**工作负载出的两个图标分别为容器命令行和容器自身log，需要时可以使用。**		![image-20240812211242039](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240812211242039.png)

### 2.Emby基本设置

1. **点击Web Portal或输入ip:设置的端口号即可进入Emby的WebUI。**

2. **进入WebUI后首先设置语言，选择中文（不想选也行）。**

   ![image-20240812211604118](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240812211604118.png)

   **刷新页面即可切换到中文界面。**

3. **设置用户名和密码，Emby介绍的很详细了，这里就不多说了。**

   ![image-20240812211711314](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240812211711314.png)

4. **下一步是新建媒体库，先跳过，等下详细说。**

5. **设置元数据语言，选中文即可。**

6. **配置远程访问，有需要设置即可。**

7. **进入首页，你将得到一个全新干净的Emby（如果你没设置媒体库的话）。点击右上角齿轮进入设置界面。**

   ![image-20240812212522782](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240812212522782.png)

8. **这是设置界面，这里只讲必要设置，其余可玩性很高，可以自行探索。**

   ![image-20240812213452384](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240812213452384.png)

9. **设置媒体库**

   1. 点击媒体库->新媒体库，新建媒体库。![image-20240814220418206](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814220418206.png)

   2. 选择内容类型，电影。

   3. 自定义名称。

      注：后面用到moviepilot后会有二级分类，如果嫌乱的话可以依照二级分类重新添加媒体库。

   4. 添加文件夹，也就是刚刚在TrueNas中映射的movie文件夹。

   5. 其余设置保持原样即可，数据刮削到时候会用moviepilot进行。

      ![image-20240814220718561](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814220718561.png)

   6. 同样新建一个电视剧的媒体库。![image-20240814220805783](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814220805783.png)

   7. 这是我后面进行二级分类后的媒体库。![image-20240814220944788](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814220944788.png)

10. **到这里Emby的基本设置就完成了，进行下一步！**

## 四、qbittorrent安装

### 1.TrueNas安装应用

1. **qbittorrent也已经加入TrueNas官方仓库，可以直接安装。安装方式与Emby相同，这里只说需要特殊设置的地方。**

2. **目录设置这里，download的目录设置是必须的，qbittorrent映射出来的路径为 /download。**

3. **如果需要其他下载路径，可以添加其他目录映射，映射出来的路径即为设置的路径，如图所示。**

   ![image-20240814223435115](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814223435115.png)![image-20240814223551972](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814223551972.png)

4. 以下是设置参考：![image-20240814224533481](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814224533481.png)![image-20240814223910819](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814223910819.png)

### 2.qbittorrent初始化

1. **第一次安装时需要用临时密码进行登录，临时密码获取在上文提到的log处查看。**![image-20240814225129289](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814225129289.png)

   ![image-20240814225255987](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814225255987.png)

2. 从TrueNas点击Web Portal进入是如果遇到提示**Unauthorized**，进入config中添加下列语句：

   （按我设置的目录来的话：/ApplicationData/qbittorrent_config/qbittorrent/qbittorrent.conf)

   emmm...解决办法不唯一，可以自己上网查查，一般都是改conf。

   我的解决方法是：**不从TrueNas进WebUI🙂**

3. **进入WebUI，点击小齿轮进行设置。**

   ![image-20240814225343641](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814225343641.png)

4. **先将语言设置成中文，不想射也行┑(￣Д ￣)┍，刷新页面。**

5. **在Web UI选项中，设置用户名和密码。同时这里可以设置白名单，免去频繁输密码。**

   ![image-20240814225557042](C:/Users/65405/AppData/Roaming/Typora/typora-user-images/image-20240814225557042.png)

6. **同样还有许多高级设置，可以自行探索。**

7. **接下来最后一步，安装MoviePilot。**

## 五、MoviePilot安装

### 1.MoviePilot简介

1. **这个东西集概览、下载、刮削、做种、辅种等各种功能于一身，基本这个东西吃透了影音方面就够用了。**

2. 官方wiki：[MoviePilot Wiki](https://wiki.movie-pilot.org/)

3. 因为该应用还未加入TrueNas官方仓库，这里需要参照其docker安装方式进行安装，其余自定义应用都可以参考本节文档。

   ![image-20240814230103097](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814230103097.png)

   s

### 2.TrueNas安装MoviePilot

1. **TrueNas中依次点击右侧应用->探索应用程序->自定义应用程序（右上角）即可进入非官方应用安装界面。**

2. **下面依次介绍各设置。**

3. **这一张图中只需要填写应用名称和镜像源。**

   **其中镜像源对应上方docker-cli安装指南截图中的最后一行。**

   > jxxghp/moviepilot

   ![image-20240814230552174](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814230552174.png)

4. **接下来是庞大的Environment设置，对应的是docker中-e设置。**

   ![image-20240814232958643](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814232958643.png)![image-20240814233016313](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814233016313.png)![image-20240814233413845](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814233413845.png)

   > 其中，PUID和GUID参考之前新设置的用户的id
   >
   > AUTH_SITE相关设置参考[配置参考 | MoviePilot Wiki (movie-pilot.org)](https://wiki.movie-pilot.org/zh/configuration)中的相关说明，需要认证站点信息的可以来找13（狗头）
   >
   > 这个设置是必须的，因为涉及到影音源的设置，不认证是不能添加下载源的。
   >
   > 其他的按照docker安装说明中填写即可。

5. **端口映射：将3000端口映射出来即可。**![image-20240814233503280](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814233503280.png)

6. **目录设置：**同样按照docker安装指南中-v项进行设置，截图为参考，具体参考官方wiki中的说明。

   需要说明的是，/download映射名不能更改，因为之前qbittorrent的默认下载路径映射的也是/download。

   如果需要不同的下载目录，需要将两个软件的目录名映射的一模一样，否则将导致下载失败。

   ![image-20240814234500494](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814234500494.png)

7. **Portal Configuration**

   这个东西我理解的就是如果设置了在应用界面就会出现一个Web Portal的按钮，需要与前面设置的端口映射一致。

   ![image-20240814234804589](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240814234804589.png)

8. **其余设置看需求自行探索。**

9. **点击安装，等待安装完毕即可，安装过程较慢，即使显示为绿色也要具体参考log判断。**

### 3.MoviePilot基础设置

1. 同样有初始密码，获取地址也在log中，登陆后记得第一时间改密码。
2. 进入WebUI，点击设定。在用户选项中更改密码。![image-20240815003347896](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815003347896.png)

#### 设定

1. **链接项设置：分别安装qbitorrent和Emby的相关地址填写**

   **qbitorrent中的自动分类管理不要点开。**

   ![image-20240815003649789](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815003649789.png)

   其中Emby的API密钥，在Emby控制台的设置中获取。

   ![image-20240815003857289](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815003857289.png)

2. **目录设置：**

   **其中下载目录最好如图分类设置，经测试不分类的话会有bug导致无法下载。**

   **媒体库目录中点开自动分类，关闭刮削元数据，元数据等下说。**

   整理模式默认复制，有辅种需要可以设置为硬链接，但因为TrueNas池设置方面的问题，硬链接还要对qb的下载路径进行额外设置，这里就不赘述了，可以联系13了解。

   ![image-20240815004742464](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815004742464.png)

3. **站点设置，适合有多个pt站用户进行自动获取站点信息**

   但经过测试，虽然cookiecloud设置好了，但还是得手动添加站点信息，所有这里就不赘述了。

4. 基本设置完成，其余设置参考官方wiki自行探索。

#### 站点管理

1. 之前部署时如果认证成功了，就可以在这里添加自己的站点，有公共站也有私有pt站，详情参考官方wiki[站点 | MoviePilot Wiki (movie-pilot.org)](https://wiki.movie-pilot.org/zh/site)

2. 添加站点需要登录后获取cookie和user-agent，用我这个教程的应该都会，所以就不赘述了。

   ![image-20240815005910917](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815005910917.png)

3. **添加成功后：点击测试如果成功，就会得到以下界面。**

   ![image-20240815005936902](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815005936902.png)

#### 插件

1. movie有庞大的插件市场，这里只介绍最需要的两个。其他的请自行探索。

2. 在插件市场中下载“**目录监控**”及“**媒体库刮削**”这两个插件。

   ![image-20240815010208698](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815010208698.png)

3. **目录监控**用来将之前已有的影音文件整理到当前的影音库。

   详细设置图里写的很详细了

   ![image-20240815024246505](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815024246505.png)

   **需要注意**的是要将旧的文件所在目录通过镜像那边映射到movepilot中才行。

   例如这样：

   ![image-20240815024434694](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815024434694.png)

   **注1：整理完成旧的东西后记得关闭这个插件，免得它定时跑占资源。**

   **注2：当然也可以有这种操作：监控目录在下载文件夹，这样通过别的方式下载的影音也可以整理了，当然这时候就不要关插件了。（非movelipot下载的不会自动整理）**

   **注3：不行！！这狗东西会在你下载的时候就进行转移，突然想起了官方wiki中提示的，需要在qbittorrent设置为下载好的文件要加后缀。也就是把这个勾打上。**

   ![image-20240815032725659](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815032725659.png)

   [文件整理 | MoviePilot Wiki (movie-pilot.org)](https://wiki.movie-pilot.org/zh/reorganize)

   ![image-20240815032757268](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815032757268.png)

4. 媒体库刮削插件等于挂上了tinymediamanager（tmm），就不用自己再起应用了。

   填写好映射的自己的媒体库目录即可。

   ![](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/image-20240815024916124.png)

5. **好啦， 到这里就可下载电影享受咯。**





完结撒花！*★,°*:.☆(￣▽￣)/$:*.°★* 。



## 六、一些随时想到的碎碎念

> 1.也就是说，这个玩意儿即使没有pt私有站用来下载，也可以作为一个集合来使用。包含了整理、概览、查看最新电影等功能，也完全值得使用。



