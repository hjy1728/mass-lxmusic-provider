# fix: 兼容MA 2.10.x Lxmusic 洛雪插件 v1.2.0 浏览支持查看排行榜和广场歌单，自动导入收藏和自建歌单
# LX Music Provider for Music Assistant

将 [LX Music（洛雪音乐）](https://github.com/xcq0607/lxserver) 的 Docker 服务端作为
**Music Assistant (MA)** 的音乐源。支持跨音源搜索、播放、歌词与歌单。

> 适用 MA 版本：2.8.x。服务端为 `lxserver`（原 LX Music 服务端 / lx-music-serve）。

## 功能特性

- **跨音源聚合搜索**：歌曲 / 歌手 / 专辑 / 歌单（酷我、酷狗、QQ、网易云、咪咕）
- **单曲播放**：自动按 `flac → 320k → 128k` 选择音质，失败自动回退其它音源
- **歌单**：可搜索「广场歌单」并查看完整曲目列表；可浏览「我的歌单」（喜爱 / 自建）
- **热门榜单浏览**：按音源查看热门歌曲
- **相似歌曲推荐**
- **歌词**：通过 MA 的歌词接口获取

## 安装

### 方式一：ma-custom-loader（推荐）

1. 在 MA 的 `music_assistant` 配置目录中安装 `ma-custom-loader` 插件加载器。
2. 将此 `lxmusic_provider` 目录放置到加载器指定的自定义插件目录：

   ```
   <config>/custom_providers/lxmusic_provider/
   ```

3. 重启 Music Assistant。

### 方式二：直接挂载到 providers 目录

将 `lxmusic_provider` 目录复制到 MA 容器内的 providers 目录并重启（容器名与路径按实际部署调整）：

```bash
docker cp lxmusic_provider music_assistant:/app/music_assistant/server/providers/lxmusic
docker restart music_assistant
```

> 若使用 bind mount 部署，直接把目录放到宿主机挂载的 `providess/lxmusic` 路径并重启容器即可。

## 配置

在 Music Assistant 的集成页面添加「LX Music」并填写：

| 配置项 | 说明 | 默认值 |
| --- | --- | --- |
| 服务端地址 | LX Music 服务端 URL | `http://localhost:9527` |
| 用户名 | 服务端登录用户名 | lxserver账号，默认`admin` |
| 密码 | 服务端登录密码 | lxserver登录密码 |
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
- 单曲 / 专辑 / 歌手详情接口缺失，详情由搜索结果聚合填充（歌手页、专辑页会回搜补全曲目）。
- 歌单曲目采用分页返回，与 MA 的 `playlists.tracks()` 分页协议对齐，可正确加载上千首的大歌单。
- 播放链接获取失败时，会自动尝试其它音源 / 音质回退。
- 服务端地址、用户名、密码均为本地配置项，根据用户自己的本地配置填写。

## 目录结构

```
lxmusic_provider/
├── manifest.json      # 插件元信息与配置项
├── __init__.py        # Provider 全部实现（配置入口、搜索、浏览、播放、歌单）
├── icon.svg           # 插件图标
├── verify_search.py   # 本地验证脚本：对 lxserver 调 songList 接口排查返回结构
└── README.md          # 本文档
```

## 本地验证

`verify_search.py` 可直接对 lxserver 发起登录与歌单搜索，确认返回结构与插件解析一致：

```bash
python3 verify_search.py [server_url] [username] [password] [keyword]
# 例：python3 verify_search.py http://localhost:9527 admin mypassword 流行
```
