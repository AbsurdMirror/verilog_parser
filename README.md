# echarts-vue

一个数据可视化显示的前端静态网站，通过修改本地json文件，可以直接在浏览器中查看可视化分析图表

## 技术栈

- vue3 + vite
- echarts
- element-ui

## 开发说明

- 使用vue加载本地json文件实现文件读取
- 使用 `npm run dev` 进行开发测试
- 使用vue开发测试时json数据存放在 `public` 文件夹下
- 使用 `npm run build` 进行生成 html/css/js 文件，生成到 `docs` 文件夹下
- `public` 文件夹在build的时候也会打包到 `docs` 文件夹下

## 使用说明

- 通过 `docs/index.html` 打开网页
- 浏览器需要开启 `--disable-web-security` 选项，以 edge 浏览器为例，运行如下命令，其中的 `xxx` 需要输入自己的目录，而edgeDevData是一个自行创建的空文件夹

```bat
& 'C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe' \
--disable-web-security \
--user-data-dir=xxx/edgeDevData \
xxx\docs\index.html
```

## 数据配置

需要按照给定格式配置 json数据。json文件在`docs/data/`下。需要遵循以下规则：

### contents.json

**该文件必须存在**，**必须在`docs/data/`目录下**，**文件名称不可修改**，一个目录文件，组织全部需要的可视化的项目

文件编写规则如下：

- `contents.json`: `Array(proj)` 一个数组,由多个object组成，每个object包含一个项目的配置信息
  - `proj`: `Object()` 每个项目的配置信息
    - `name`: `string` | 项目名称
    - `path`: `string` | 项目数据的存放的文件夹
    - `hasData`: `bool` | *已弃用*
    - `children`: `Array(proj)` | 子项目集合， **Note**: 包含`children`的`proj`的`path`暂不支持识别。

### versions.json

**每个项目必须存在一个该文件**，**必须在项目的`path`的目录下**，**文件名称不可修改**，一个版本记录文件，组织项目的全部版本

文件编写规则如下：

- `versions.json`: `Array(versionObj)` | 一个数组,由多个object组成，每个object包含一个项目的版本的信息
  - `versionObj`: `Object` | 每个版本的信息
    - `version`: `string` | 版本的编号或名称
    - `path`: `string` | 版本的统计信息的json文件的路径
    - `hasData`: `bool` | *已弃用*

### data.json

存放版本的统计信息的json文件,文件名称可自定义，文件通过对应项目的`versions.json`进行索引

文件编写规则如下：

- `data.json`: `Object()` | 版本的统计信息
  - `echart_main`: `Object()` | 显示在网页上的一个主要图表，只能采用条形堆叠图
    - `height`: `string` | 图表的高度，格式为 ??px，可选，默认值为 `"500px"`
    - `title`: `string` | 图表的标题
    - `layers`: `Array(string)` | 图表的Y轴，Array从左到右对应图表从下到上
    - `totalValue`: `int` | 总数量值，用于计算百分比
    - `data`: `Array(dataObj)` | 要显示的各个分量信息
      - dataObj: `Object()` | 要显示的各个分量信息
        - `name`: `string` | 分量名称
        - `value`: `int` | 分量的数值
        - `layer`: `string` | 所属Y轴的位置
  - `cards`: `Array(Card)` | 显示在网页右侧的两列小卡片的配置，每个`Card`对应一个卡片
    - `Card`: `Object` | 一个卡片的配置信息
    - `type`: `string` | 卡片的类型，不同类型的属性配置也不同
    - `{type: 'text', ...}`: `Object` | 文本类型卡片
      - `head`: `string` | 卡片标题
      - `body`: `string` | 卡片的文本内容
    - `{type: 'image', ...}`: `Object` | 图片类型卡片
      - `head`: `string` | 卡片标题
      - `path`: `string` | 图片路径
    - `{type: 'gauge-percent'}`: `Object` | 百分比数据类型卡片
      - `head`: `string` | 卡片标题
      - `name`: `string` | 数据名称
      - `percent`: `int/float` | 百分比数据值，0~100，注意，当`percent` 和 `total`、`part`同时存在时，选择`percent`的值
      - `total`: `int/float` | 用于百分比数据计算，分母值
      - `part`: `int/float` | 用于百分比数据计算，分子值
    - `{type: 'gauge-number'}`: `Object` | 数值数据类型卡片
      - `head`: `string` | 卡片标题
      - `name`: `string` | 数据名称
      - `value`: `int/float` | 数据数值
      - `min`: `int/float` | 数据取值范围最小值，默认值 0
      - `max`: `int/float` | 数据取值范围最大值，默认值 100
    - `{type: 'gauge-number'}`: `Object` | 描述列表类型卡片
      - `head`: `string` | 卡片标题
      - `items`: `Array(item)` | 描述项的数组
        - `item`: `Object` | 描述项
          - `label`: `string` | 描述项的名称
          - `value`: `any` | 描述项的值
          - `isTag`: `bool` | 是否显示为tag标签形式






