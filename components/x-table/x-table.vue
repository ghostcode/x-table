<template>
  <view class="e-table" :class="bordered ? 'table-border' : ''">
    <view class="table" :style="[scrollHeight, tableStyle]">
      <!-- 表头 -->
      <view class="tr t-head" :style="[headFixedStyle]">
        <!-- 固定到左侧的列 -->
        <view
          v-if="columnsFixedLeft.length > 0"
          class="columns-fixed-left"
          :style="[columnsFixedLeftStyle]"
        >
          <view
            class="th fixed"
            v-for="(col, index) in columnsFixedLeft"
            :key="col.key"
            :style="[col.style]"
            >{{ col.title }}</view
          >
        </view>

        <!-- 非固定列 -->
        <view
          class="th"
          v-for="(col, index) in columnsNormalHeader"
          :key="col.key"
          :style="[col.style]"
          >{{ col.title }}</view
        >

        <!-- 固定到右侧的列 -->
        <view
          v-if="columnsFixedRight.length > 0"
          class="columns-fixed-right"
          :style="[columnsFixedRightStyle]"
        >
          <view
            class="th"
            v-for="(col, index) in columnsFixedRight"
            :key="col.key"
            :style="[col.style]"
            >{{ col.title }}</view
          >
        </view>
      </view>

      <!-- 表体 -->
      <view class="t-body" :style="[bodyStyle]">
        <view
          class="tr"
          v-for="(data, index) in dataSourceAll"
          :key="index"
          @click="emitRowClick(data, index)"
          :style="[data.style]"
        >
          <!-- 固定到左侧的列 -->
          <view
            v-if="columnsFixedLeft.length > 0"
            class="columns-fixed-left"
            :style="[columnsFixedLeftStyle, data.style]"
          >
            <view
              class="td"
              v-for="(col, i) in columnsFixedLeft"
              :key="data.key"
              :style="[col.style]"
            >
              <!-- #ifdef MP-WEIXIN-->
              <!-- 这个问题还没有解决，小程序暂时无法使用插槽-->
              <view>{{ data[col.dataIndex] }} </view>
              <!-- #endif -->
              <!-- #ifdef H5 -->
              <view v-if="col.scopedSlots && col.scopedSlots.customRender">
                <slot
                  :name="col.scopedSlots.customRender"
                  :record="data"
                  :text="data[col.dataIndex]"
                  :index="index"
                ></slot>
              </view>
              <view v-else>{{ data[col.dataIndex] }}</view>
              <!-- #endif -->
            </view>
          </view>

          <!-- 非固定列 -->
          <view
            class="td"
            v-for="(col, i) in columnsNormal"
            :key="data.key"
            :style="[col.style]"
          >
            <!-- #ifdef MP-WEIXIN-->
            <!-- 这个问题还没有解决，小程序暂时无法使用插槽-->
            <view>{{ data[col.dataIndex] }} </view>
            <!-- #endif -->
            <!-- #ifdef H5 -->
            <view v-if="col.scopedSlots && col.scopedSlots.customRender">
              <slot
                :name="col.scopedSlots.customRender"
                :record="data"
                :text="data[col.dataIndex]"
                :index="index"
              ></slot>
            </view>
            <view v-else>{{ data[col.dataIndex] }}</view>
            <!-- #endif -->
          </view>

          <!-- 固定到右侧的列 -->
          <view
            v-if="columnsFixedRight.length > 0"
            class="columns-fixed-right"
            :style="[columnsFixedRightStyle, data.style]"
          >
            <view
              class="td"
              v-for="(col, i) in columnsFixedRight"
              :key="data.key"
              :style="[col.style]"
            >
              <!-- #ifdef MP-WEIXIN-->
              <!-- 这个问题还没有解决，小程序暂时无法使用插槽-->
              <view>{{ data[col.dataIndex] }} </view>
              <!-- #endif -->
              <!-- #ifdef H5 -->
              <view v-if="col.scopedSlots && col.scopedSlots.customRender">
                <slot
                  :name="col.scopedSlots.customRender"
                  :record="data"
                  :text="data[col.dataIndex]"
                  :index="index"
                ></slot>
              </view>
              <view v-else>{{ data[col.dataIndex] }} </view>
              <!-- #endif -->
            </view>
          </view>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
// todo:1.固定列样式；2.圆角
// 单元格宽度
const CellWidth = 120;

function getWidth(columns = []) {
  // todo 无宽度校验
  return columns.reduce((prev, current, index, arr) => {
    return prev + current.width;
  }, 0);
}

// 兼容传递的值里包含 %
// 若包含则不加 'rpx'，值空则默认 100%
function getSize(value) {
  if (value) {
    if (!value.toString().includes("%")) {
      value = value + "rpx";
    }
  } else {
    value = "100%";
  }
  return value;
}

export default {
  name: "etable",
  props: {
    // 设置列
    columns: {
      required: true,
      type: Array,
      default: () => {
        return [];
      },
    },
    // 数据源
    dataSource: {
      required: true,
      type: Array,
      default: () => {
        return [];
      },
    },
    // 是否边框
    bordered: {
      required: false,
      type: Boolean,
      default: false,
    },
    // 表格样式
    tableStyle: {
      required: false,
      type: Object,
      default() {
        return null;
      },
    },
    // 行样式（不包括表头行）
    rowStyle: {
      required: false,
      type: Object,
      default() {
        return null;
      },
    },
    // 表头样式
    headStyle: {
      required: false,
      type: Object,
      default() {
        return null;
      },
    },
    // 滚动需要设置 x , y 值
    scroll: {
      required: false,
      type: Object,
      default() {
        return null;
      },
    },
  },
  computed: {
    dataSourceAll() {
      return this.dataSource.map((item, index) => {
        // todo 待优化
        if (
          this.rowStyle &&
          (this.rowStyle.width || this.rowStyle.height || this.rowStyle.border)
        ) {
          console.error("禁止设置影响盒模型的样式（width/height/border 等）");
        }
        if (this.rowStyle) {
          const { odd, even, ...other } = this.rowStyle;
          if (index % 2 === 0) {
            // 偶数行
            item.style = {
              ...other,
              ...even,
            };
          } else {
            // 奇数行
            item.style = {
              ...other,
              ...odd,
            };
          }
        }

        return item;
      });
    },
    //左侧固定列
    columnsFixedLeft() {
      return this.columns
        .filter((item) => item.fixed === "left")
        .map((item, index) => {
          if (item.width) {
            item.style = {
              ...item.style,
              flex: `0 0 ${item.width}rpx`,
            };
          } else {
            console.error("固定列必须设置 width 属性，否则会导致列对不齐！");
          }
          return item;
        });
    },
    // 不包含固定列
    columnsNormal() {
      return this.columns
        .filter((item) => !item.fixed)
        .map((item, index) => {
          if (item.width) {
            item.style = {
              ...item.style,
              flex: `0 0 ${item.width}rpx`,
            };
          }
          return item;
        });
    },
    // 不包含：固定 和 colSpan = 0 的列
    columnsNormalHeader() {
      return this.columns
        .filter((item) => !item.fixed && item.colSpan !== 0)
        .map((item, index) => {
          item = JSON.parse(JSON.stringify(item));

          if (item.width) {
            item.style = {
              ...item.style,
              flex: `0 ${item.colSpan ? item.colSpan : 0} ${item.width}rpx`,
            };
          }

          if (item.colSpan) {
            item.style = {
              ...item.style,
              flex: `${item.colSpan ? item.colSpan : 0} 0 auto`,
            };
          }

          return item;
        });
    },
    //右侧固定列
    columnsFixedRight() {
      return this.columns
        .filter((item) => item.fixed === "right")
        .map((item, index) => {
          if (item.width) {
            item.style = {
              ...item.style,
              flex: `0 0 ${item.width}rpx`,
            };
          } else {
            console.error("固定列必须设置 width 属性，否则会导致列对不齐！");
          }
          return item;
        });
    },
    columnsFixedLeftStyle() {
      // 获取所有左侧固定列的宽度之和
      let leftWidth = getWidth(this.columnsFixedLeft);
      return {
        flex: `0 0 ${leftWidth}rpx`,
      };
    },
    scrollHeight() {
      let scrollHeight = this.scroll?.y;

      scrollHeight = getSize(scrollHeight);

      return {
        height: scrollHeight,
      };
    },
    columnsFixedRightStyle() {
      // 获取所有右侧固定列的宽度之和
      let rightWidth = getWidth(this.columnsFixedRight);
      return {
        flex: `0 0 ${rightWidth}rpx`,
      };
    },
    headFixedStyle() {
      let scrollHeight = this.scroll?.y;
      let scrollWidth = this.scroll?.x;
      let style = {};

      // todo 待优化
      if (
        this.headStyle &&
        (this.headStyle.width || this.headStyle.height || this.headStyle.border)
      ) {
        console.error("禁止设置影响盒模型的样式（width/height/border 等）");
      }

      scrollHeight = getSize(scrollHeight);
      scrollWidth = getSize(scrollWidth);

      // 若设置 y 值，则固定表头。
      if (scrollHeight) {
        style = {
          position: "sticky",
          top: 0,
        };
      }
      return {
        ...style,
        ...this.headStyle,
        width: scrollWidth,
      };
    },
    bodyStyle() {
      let scrollWidth = this.scroll?.x;

      scrollWidth = getSize(scrollWidth);

      return {
        width: scrollWidth,
      };
    },
  },
  data() {
    return {};
  },
  created() {
    console.log("table", this.tableStyle);
  },
  methods: {
    emitRowClick(record, index) {
      console.log("record", record);
      this.$emit("rowClick", record, index);
    },
  },
};
</script>

<style lang="scss" scoped>
@mixin ellipse {
  white-space: nowrap;
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

.e-table {
  max-width: 100%;
  height: 100%;
  overflow: hidden;
}

.table {
  overflow: scroll;
  background-color: #fff;
  .tr {
    display: flex;
  }
  .th,
  .td {
    box-sizing: border-box;
    height: 72rpx;
    line-height: 72rpx;
    padding: 0 10rpx;
    text-align: center;
    flex: 1;
    @include ellipse;
  }
  .t-head {
    background-color: #fff;
    z-index: 1;
  }
}

.table-border {
  .table {
    border-top: 1px solid #ddd;
    border-left: 1px solid #ddd;
  }
  .th,
  .td {
    border-bottom: 1px solid #ddd;
    border-right: 1px solid #ddd;
  }
}

.columns-fixed-left {
  position: sticky;
  left: 0;
  display: flex;
  background-color: #fff;
}

.columns-fixed-right {
  position: sticky;
  right: 0;
  display: flex;
  background-color: #fff;
}
</style>
