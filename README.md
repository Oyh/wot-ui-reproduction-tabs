# wd-tabs sticky + animated 宽度累加

`wd-tabs` 同时开启 `sticky` 和 `animated` 时，访问过的页签越多，tabs 自身宽度越大，直到超过视口宽度。

- Wot UI 2.4.0，2.3.2 上表现一致
- 平台：H5、微信小程序，两端都复现
- uni-app 3.0.0 + Vue 3

## 运行

```bash
pnpm install
pnpm dev:h5          # http://localhost:5173
pnpm dev:mp-weixin   # 产物在 dist/dev/mp-weixin，导入微信开发者工具
```

复现代码只有一个页面：`src/pages/index/index.vue`。

```vue
<WdTabs v-model="active" sticky animated>
  <WdTab v-for="i in 10" :key="i" :title="`tab ${i}`">
    <view style="font-size: 48px">
      第 {{ i }} 页：这是一段普通的示例文字。
    </view>
  </WdTab>
</WdTabs>
```

## 复现步骤

1. 打开首页，把浏览器视口调成手机宽度
2. 多点几个页签，顺序无所谓；没访问过的页签不会渲染，也就不会往宽度里算
3. 看页面上那行读数，前半是 tabs 的实际宽度，后半是视口宽。开始时两个数字相等，访问的页签多了前一个就超过后一个

只开 `sticky` 或只开 `animated` 时，前半始终等于后半。

视口 402px，按 1 到 8 顺序访问时的读数：

| 已访问页签数 | `.wd-tabs` 宽度 |
| --- | --- |
| 1 ~ 4 | 402 |
| 5 | 480 |
| 6 | 576 |
| 7 | 672 |
| 8 | 768 |

视口宽度不同，具体数值跟着变，结论一样：一旦超过视口，后面每多访问一个页签就继续加宽。

期望：切换页签时 tabs 宽度始终等于视口宽。

## 对照

- 只开 `sticky`、不开 `animated`：宽度恒为视口宽
- 只开 `animated`、不开 `sticky`：宽度恒为视口宽
- 两者同时开：出现上面的增长

宽度增长发生在 `wd-sticky-box` 内部由 `wd-resize` 渲染的 `.wd-resize__container` 上，那是一个 `position: absolute` 的收缩宽容器，`.wd-tabs` 宽度与其一致。

示例里的字号调到 48px 只是为了让宽度更快累积到超过视口；正常字号下页数更多时同样出现。

## 说明

仓库基于官方 `wot-starter` 模板生成，删掉了模板自带的演示页，只保留首页用于复现。
