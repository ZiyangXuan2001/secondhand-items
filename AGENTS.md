# 二手物品清单

这是用户的二手物品记录项目。保持简单：原生 HTML + JSON + 媒体文件，可部署到 GitHub Pages 或任意静态托管服务。不要引入框架、构建步骤、数据库服务或后台，除非用户另有要求。

## 更新物品

- `data/items.json` 是唯一的物品数据源；`media/` 保存照片、视频；`index.html` 负责展示。
- 用户发来名称、价格和媒体时，在这里直接更新；不要求用户自己编辑 JSON。
- 每件物品使用稳定、唯一的 `id`，建议 `item-001` 等。媒体放入 `media/<id>/`，使用相对于网站根目录的路径，例如 `media/item-001/front.jpg`，不要以 `/` 开头（兼容 GitHub 项目页面）。
- 字段：`id`、`name`、`price`（数字或 null）、`currency`（如 USD/CNY）、`status`（available/sold）、`description`（可空）、`media`（数组；每项包含 type=image/video、src、alt）。视频可选 `poster`。
- 价格和币种以用户提供的信息为准；缺价格用 null 显示“待定价”，不要猜测或填 0。缺币种时使用项目现有惯例，没有惯例则询问用户。
- 用户说“卖掉了”，将对应记录的 `status` 改为 `sold`；保留价格、照片和描述，不删除记录。网页会自动将整行变淡，并把已售出物品放到后面。
- 用户说“恢复在售”，将 `status` 改回 `available`。
- 所有物品的第一张图片必须为对应型号、颜色的官方产品图（优先品牌官网，或可信零售商产品页中的官方展示图），用户实拍和视频随后保留。先核对型号，不使用近似型号、买家实拍或 AI 生成图冒充官方图；未找到可靠图片时说明缺口。官方图下载到 `media/<id>/official.jpg`（扩展名匹配实际格式），记录 `official: true`、`source_name`、`source_url`、`original_url`，页面标注“官方图/非实拍”。不向用户已清空的 Reference 列自动添加图片来源，来源随媒体单独保存。
- 不添加虚构或演示物品。没有真实物品时保持空数组。
- 不把地址、电话、私聊内容等个人信息写入公开清单，除非用户明确要求。
- 对 HEIC 等浏览器不通用格式保留原件，按用户需求另存可展示版本；不要只改后缀。大视频优先使用用户提供的可直接播放链接，不擅自公开上传第三方。

## 页面与参考信息

- 页面按用户要求：每件物品一行，列顺序为名字、价格、取货时间、人工描述、视频/照片、Reference。窄屏保留表格，可横向滚动，不改回卡片墙。
- `description` 只存用户提供或确认的人工描述；缺少时留空，不自行编写成色、配件或使用体验。
- `references` 为可选数组；每条含 `text`、`source`、`verified`（布尔值）、`url`（原始来源 HTTP(S) 链接或 null）。保留来源和未核实标记，不编造链接。参考价不能覆盖实际售价。

## 检查与交付

- 更新后检查 JSON 有效、id 唯一、金额非负、状态有效、本地媒体文件存在。
- 页面无外部脚本依赖，不需要 npm。预览可用 `python3 -m http.server 8000 --bind 127.0.0.1`。
- GitHub 远程为 https://github.com/ZiyangXuan2001/secondhand-items.git，发布分支为 main，GitHub Pages 从根目录发布。用户已确认首次公开发布；后续发布遵循用户授权，部署成功前不要声称更新已上线。

## 取货时间

- `pickup` 为对象：`status` 为 `pending`（待确认）、`anytime`（随时取）、`scheduled`（指定最早日期）。`earliest_date` 仅在 scheduled 时填写有效的 YYYY-MM-DD 日期，其余为 null。未指定取货安排时用 pending，不擅自标为随时取。
- 用户已删除定金列；目前不显示或计算定金，除非用户重新要求。

- 桌游的 Reference 只保留产品链接，不展示五折、半价、购买价或定价过程。链接记录保留 source/url/verified，text 为空时页面仅显示链接。用户最新明确报价优先于此前半价规则。

- 发布隐私：`local-private/`、`media/item-001/demo.mp4` 和 `media/item-001/demo-poster.jpg` 不公开（视频有账号和聊天列表）。发布只上传 Git 未忽略的文件；原始照片元数据备份也留在 local-private。
