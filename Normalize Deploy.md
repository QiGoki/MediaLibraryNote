# TrueNas及三应用规范化部署参数

> 主要记录TrueNas及Emby、Qbittorrent、MovePilot三个应用部署时的参数设置，主要是路径设置。
>
> 为了方便查找，与使用书册分开记录。

## 目录结构

```yaml
-- main: #（主池）
	-- ApplicationData: #（各应用config存放文件夹）
		-- emby_config
		-- qbittorrent_config
		-- ...
	-- data:
		-- Media: #（影音库主文件夹）
			-- movie #（电影）
			-- tv #（电视剧）
			-- downloads:
				-- move #（movepilot下载用）
				-- tv #（movepilot下载用）
				-- unclassified #（qb影音下载临时文件夹）
		-- KodCloud
		-- ... ...(Other app)
```

## TrueNas

> 主要创建好目录结构及用户。
>
> 不再赘述，目录结构按上文创建好即可。
>
> 用户按需创建，记录好uid和密码即可。

## Emby

> 主要是部署镜像时的目录映射，以及部署好后的媒体库建立。

### 目录映射

- config映射至相应config目录

- 为图方便，自定义映射只映射一个目录。

  ```sh
  /mnt/main/data/Media:/data
  ```

### 媒体库建立

- 初始化阶段，只映射出两个媒体库，分别为电影和电视剧。即/data/movie、/data/tv。
- 后期如果媒体数量增加，再考虑按movepilot的分类重新映射媒体库。

## Qbittorrent

> 包括部署时目录映射，以及部署好后的相关设置。

### 目录映射

- 自带/downloads映射弃用，但又为必填项，因此映射至/mnt/main/media/downloads即可。

- config映射至相应config目录

- **映射movepilot下载目录**

  ```sh
  # 映射目录名必须一致
  /mnt/main/data/Media/downloads:/moviepilot_downloads
  ```

- **映射非movepilot下载目录**

  ```sh
  /mnt/main/data/Media/downloads/unclassified:/unclassified_downloads
  ```

### 部署后设置

- **设置用户名及密码**

- 下载设置：

  - 打开**为不完整的文件添加扩展名 .!qB**设置。

  - 设置默认下载路径为**unclassified_downloads。**

    ![image-20240819160322276](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/D%3A/git/MediaLibraryNote/Normalize%20Deploy/image-20240819160322276.png)

## MoviePilot

> 包括部署时路径映射及部署后设置

### 目录映射

- **映射movepilot下载目录**

  **必须与qb的设置一致**，一模一样，否则会导致下载异常。

  ```sh
  # 映射目录名必须一致
  /mnt/main/data/Media/downloads:/moviepilot_downloads
  ```

- 映射moviepilot媒体目录

  ```
  /mnt/main/data/Media:/media
  ```


### 部署后设置

#### 设定->目录

如果按照本流程来的，**请严格按照下图设置。**

之前目录结构有变化的，替换成相应变化。

![image-20240819173051617](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/D%3A/git/MediaLibraryNote/Normalize%20Deploy/image-20240819173051617.png)

#### 插件

##### 目录监控

如果按照本流程来的，**请严格按照下图设置。**

之前目录结构有变化的，替换成相应变化。

![image-20240819173420879](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/D%3A/git/MediaLibraryNote/Normalize%20Deploy/image-20240819173420879.png)

##### 媒体库刮削

如果按照本流程来的，**请严格按照下图设置。**

之前目录结构有变化的，替换成相应变化。

![image-20240819173506487](https://cdn.jsdelivr.net/gh/QiGoki/MADPic@Pic/Pic/D%3A/git/MediaLibraryNote/Normalize%20Deploy/image-20240819173506487.png)



















