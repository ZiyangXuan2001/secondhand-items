# 二手物品清单

一个无需安装依赖、无需数据库服务的静态物品簿。照片、视频和价格跟随文件保存，每件物品占一行，按「名字｜价格｜取货时间｜人工描述｜视频/照片｜Reference」排列；已售出整行变淡并排在后面。

## 平时怎么用

直接在 Codex 对话中发照片、视频和信息，例如：

- “这把椅子 25 美元，帮我加进去。”
- “椅子卖掉了。”
- “台灯改成 10 美元。”

助手更新 `data/items.json` 和 `media/`；你不用自己改代码。网页是展示清单，不能直接在网页里编辑。

## 文件

- `index.html`：完整页面，支持搜索、筛选、照片放大和视频播放。
- `data/items.json`：物品数据，后续直接在这里添加或更新。
- `media/`：本地照片、视频。
- `AGENTS.md`：给后续助手的更新约定。

单件物品结构（仅作说明，不会出现在页面）：

```json
{
  "id": "item-001",
  "name": "椅子",
  "price": 25,
  "currency": "USD",
  "status": "available",
  "description": "自取，状态良好",
  "media": [
    { "type": "image", "src": "media/item-001/front.jpg", "alt": "椅子正面" },
    { "type": "video", "src": "media/item-001/demo.mp4", "alt": "实拍视频" }
  ]
}
```

`price` 为 null 时显示“待定价”；价格与币种都未确认时，`currency` 也可暂存为 null，填写价格时必须补齐币种。`status` 改为 `sold` 即表示售出。媒体路径使用相对路径，也可用可直接访问的 HTTPS 图片/视频地址（普通分享页面不等于视频地址）。

## 本地查看

在此目录运行：

```sh
python3 -m http.server 8000 --bind 127.0.0.1
```

浏览器打开 http://127.0.0.1:8000 。不要双击 HTML 打开，因为浏览器通常不允许本地 HTML 直接读取 JSON。

## 放到网站上

这是普通静态文件，可直接放到 GitHub Pages 或其他支持静态文件的托管平台，无需构建。仓库：https://github.com/ZiyangXuan2001/secondhand-items 。网站地址：https://ziyangxuan2001.github.io/secondhand-items/ （首次发布需等待 GitHub Pages 完成部署）。

发布前检查照片是否包含地址、个人文件等隐私信息。公开网站上的媒体可能被任何访客下载。私有 GitHub 仓库不代表 Pages 页面也是私有。

GitHub Pages 的具体启用步骤以 GitHub 当前设置为准。配置好后，助手更新文件并推送到用于托管的分支即可同步页面。

## 描述与参考信息

`description` 保存用户提供或确认的人工描述，未提供时留空。`references` 为数组，每条含 `text`、`source`、`verified` 与可空的 `url`。转述的 GPT 价格不视为核实过的成交信息；有原始来源后可补链接。参考信息与实际售价分开保存。

照片倒置时，可在对应 `media` 项设置 `"rotation": 180`，仅在页面的缩略图和放大预览中转正，不修改原始文件。

所有物品以对应型号的官方产品图作为第一张，之后保留实拍和视频。官方图在媒体记录中标记 `official: true` 并保存来源，页面会标注“官方图”；该规则已记录在 AGENTS.md。

## 取货时间

`pickup` 保存取货安排：`{"status":"anytime","earliest_date":null}` 表示随时取；`{"status":"scheduled","earliest_date":"2026-09-15"}` 表示最早该日取；未指定用 `{"status":"pending","earliest_date":null}`，显示“待确认”。日期用 YYYY-MM-DD，不自动推测。



LG 演示视频含个人账号和聊天列表，目前不公开；原件与照片原始元数据备份仅保存在被 Git 忽略的 `local-private/`。不得将该目录或被忽略的视频上传到公开仓库。

## 可议价

用户明确指定的物品设置 `negotiable: true`，名称后显示绿色「可议价」标签。未指定或为 `false` 时不显示，售出后隐藏标签。

## 分类

每件物品的 `category` 保存类别：工作用品、生活电器、家具收纳、桌游玩具、乐器或其他。名称下展示分类标签，顶部可点击类别筛选；分类、搜索和出售状态可以组合使用。同类默认相邻，已售出仍放最后。
