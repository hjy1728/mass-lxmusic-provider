# LX Music Provider for Music Assistant

将 [LX Music（洛雪音乐）](https://github.com/xcq0607/lxserver) 的 Docker 服务端作为
**Music Assistant (MA)** 的音乐源。支持跨音源搜索、播放与歌词获取。

## 功能特性

- 跨音源聚合搜索（酷我 / 酷狗 / QQ / 网易云 / 咪咕）
- 单曲播放（自动选择音质，失败回退下载代理）
- 热门榜单浏览
- 相似歌曲推荐

## 安装

### 方式一：ma-custom-loader（推荐）

1. 在 MA 的 `music_assistant` 配置目录中安装 `ma-custom-loader` 插件加载器。
2. 将此 `lxmusic_provider` 目录放置到加载器指定的自定义插件目录：

   ```
   <config>/custom_providers/lxmusic_provider/
   ```

3. 重启 Music Assistant。

### 方式二：直接挂载到 providers 目录

将 `lxmusic_provider` 目录复制到 MA 容器内的 providers 目录并重启：

```bash
docker cp lxmusic_provider music_assistant:/app/music_assistant/server/providers/lxmusic
docker restart music_assistant
```

## 配置

在 Music Assistant 的集成页面添加「LX Music」并填写：

| 配置项 | 说明 | 默认值 |
| --- | --- | --- |
| 服务端地址 | LX Music 服务端 URL | `http://localhost:9527` |
| 用户名 | 服务端登录用户名 | `admin` |
| 密码 | 服务端登录密码 | 空 |
| 默认音源 | 获取播放链接优先音源 | `wy` |
| 搜索音源 | 搜索轮询的音源列表 | `kw,kg,tx,wy,mg` |

音源代码对照：

| 代码 | 音源 |
| --- | --- |
| `kw` | 酷我 |
| `kg` | 酷狗 |
| `tx` | QQ 音乐 |
| `wy` | 网易云音乐 |
| `mg` | 咪咕音乐 |

## 说明与限制

- LX Music 服务端**没有标准的收藏/歌单 API**，因此「我的音乐库」同步返回空。
- 单曲/专辑/歌手详情接口缺失，详情由搜索结果填充。
- 播放链接获取失败时，会自动回退到 `/api/music/download` 下载代理。

## 目录结构

```
lxmusic_provider/
├── manifest.json      # 插件元信息与配置项
├── __init__.py        # 配置入口与连接测试
├── provider.py        # 核心 Provider 实现
├── icon.svg           # 插件图标
└── README.md          # 本文档
```
