# 使用

```
import XTable from "@/components/x-table/x-table.vue";

<ETable
  :columns="columns"
  :dataSource="dataSource"
>

  <template v-slot:index="{record,text,index}">
    <view :style="{backgroundColor:'#71d5a1',color:'#fff'}">{{ index + 1}}</view>
  </template>

  <template v-slot:typeName="{record,text,index}">
    <view :style="{color:'#f29100'}">{{text}}</view>
  </template>

  <template v-slot:action="actionProps">
    <u-button size="mini" type="error">删除</u-button>
  </template>

</ETable>
```

# 展示

![image](https://github.com/ghostcode/text2img/assets/745181/3adc8c83-bbaa-4754-8e9f-05a31b65e973)

# 参数

## columns

**列描述对象，是 columns 中的一项**

| 参数        | 说明                                                                              | 类型             | 默认值 | 版本  |
| ----------- | --------------------------------------------------------------------------------- | ---------------- | ------ | ----- |
| title       | 列头显示文字                                                                      | string           | ""     |
| dataIndex   | 列数据在数据项中对应的 key                                                        | string           | -      |
| key         | Vue 中使用的 key                                                                  | string           | -      |
| width       | 列宽                                                                              | string / number  | -      |
| fixed       | 列是否固定                                                                        | "left" / "right" | -      |
| colSpan     | 表头列合并,设置为 0 时，不渲染                                                    | number           | -      | 1.2.0 |
| style       | 列样式（columns 中 width 属性的优先级高于此处设置的 width 样式）                  | object           | -      |
| scopedSlots | 作用域插槽，配置支持 slot-scope 的属性，如：`scopedSlots: { customRender: 'XXX'}` | object           | -      | 1.1.0 |

注: 小程序不支持[作用域插槽](https://zh.uniapp.dcloud.io/tutorial/vue-components.html#%E5%B0%8F%E7%A8%8B%E5%BA%8F%E4%B8%8D%E6%94%AF%E6%8C%81%E5%88%97%E8%A1%A8)。

```javascript
columns = [
  {
    title: "序号",
    dataIndex: "index",
    key: "index",
    width: 80,
    scopedSlots: {
      customRender: "index",
    },
  },
  {
    title: "名称",
    dataIndex: "name",
    key: "name",
    colSpan: 2,
  },
  {
    title: "功率(kWh)",
    dataIndex: "power",
    key: "power",
    colSpan: 0,
  },
  {
    title: "型号",
    dataIndex: "modelName",
    key: "modelName",
  },
  {
    title: "类型",
    dataIndex: "typeName",
    key: "typeName",
    width: 80,
    scopedSlots: {
      customRender: "typeName",
    },
  },
  {
    title: "操作",
    width: 320,
    scopedSlots: {
      customRender: "action",
    },
  },
];
```

## dataSource

**数据源**

| 参数 | 说明              | 类型   | 默认值 |
| ---- | ----------------- | ------ | ------ |
| 字段 | 与 columns 列对应 | string | -      |

```javascipt
dataSource = [
    {
      key: '1',
      name:'定速1'
      modelName: '32#',
      power:0.74,
      typeName: '定速1',
    },
    {
      key: '2',
      name:'定速2'
      power:0.9,
      modelName: '33#',
      typeName: '定速1',
    },
]
```

## bordered

边框

| 参数     | 说明         | 类型    | 默认值 |
| -------- | ------------ | ------- | ------ |
| bordered | 是否显示边框 | boolean | false  |

## tableStyle

**表格样式**

| 参数     | 说明       | 类型            | 默认值 |
| -------- | ---------- | --------------- | ------ |
| CSS 属性 | CSS 属性值 | string / number |

例子

```javascript
tableStyle = {
  width: "300rpx",
  height: "400rpx",
};
```

## rowStyle

**行样式**

| 参数     | 说明                                            | 类型            | 默认值 |
| -------- | ----------------------------------------------- | --------------- | ------ |
| CSS 属性 | CSS 属性值                                      | string / number | -      |
| even     | 偶数行样式（优先级高于上面 CSS 属性设置的样式） | object          | -      |
| odd      | 奇数行样式（优先级高于上面 CSS 属性设置的样式） | object          | -      |

**注：禁止设置影响盒模型的样式（width/height/border 等）**

**例子**

```javascript
rowStyle = {
    color:'red',
    backgroundColor:'blue'
    odd:{
      color:'green', // 优先级高于前面设置的 red
      fontSize:'20rpx',
    },
    even:{
      backgroundColor:'green', // 优先级高于前面设置的 blue
    }
}
```

## headStyle

**表头样式**

| 参数     | 说明       | 类型            | 默认值 |
| -------- | ---------- | --------------- | ------ |
| CSS 属性 | CSS 属性值 | string / number | -      |

**注：禁止设置影响盒模型的样式（width/height/border 等）**

**例子**

```javascript
headStyle = {
  color: "red",
  backgroundColor: "blue",
};
```

## scroll

**滚动**

| 参数 | 说明                                   | 类型   | 默认值 |
| ---- | -------------------------------------- | ------ | ------ |
| x    | 若需横向滚动，则设置横向滚动区域的宽度 | number | 100%   |
| y    | 若需竖向滚动，则设置纵向滚动区域的高度 | number | 100%   |

**注：此处设置的是滚动条的尺寸，而非表格的尺寸。**

**例子**

```javascript
scroll = {
  x: 1000,
  // y:1000,
};
```
