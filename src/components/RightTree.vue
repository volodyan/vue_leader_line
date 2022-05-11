<template>
  <div class="TreeRight" v-if="showTree">
    <div class="childs">
      <div
        class="child"
        v-for="(item, index) in list"
        :key="item.id + '-child-' + index"
      >
        <div
          class="child-item"
          :style="{
            marginRight:
              item.children && item.children.length > 1 ? '20px' : '',
          }"
        >
          <div class="childname" :id="item">
            <div class="content-box" :ref="item.id" :id="item.id">
              {{ item.id }}
              <div
                v-for="(itemshow, index3) in showfields"
                :key="'itemshow' + index3"
              >
                {{ itemshow.name }}{{ item[itemshow.key] }}
              </div>
            </div>
            <div style="width: 30px" class="mydiv2"></div>
            <div
              class="position-top"
              v-if="isFirst(item.id) && domready"
              :style="position_top(item.id, 'top')"
            ></div>
            <div
              class="position-top"
              v-if="isLast(item.id)"
              :style="position_top(item.id, 'bottom')"
            ></div>
          </div>
          <div style="width: 160px" class="MyDiv"></div>
        </div>
        <!-- 递归组件展示子节点 -->
        <div
          class="child-children"
          v-if="item.children && item.children.length"
        >
          <RightTree :list="item.children" :showfields="showfields" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import LeaderLine from "leader-line-vue";
export default {
  name: "RightTree",
  components: {},
  data() {
    return {
      domready: false,
      lines: [],
    };
  },
  create() {},
  props: {
    list: {
      type: Array,
      default: () => [],
    },
    showfields: {
      type: Array,
      default: () => [],
    },
  },
  mounted() {
    this.$nextTick(() => {
      this.domready = true;
      this.drawArrowLine();
    });
  },
  computed: {
    /**
     * 是否展示树计算属性
     */
    showTree() {
      return this.list && this.list.length;
    },
  },
  beforeDestroy() {
    /**
     * 离开页面时销毁所有line
     */
    if (this.lines && this.lines.length) {
      this.lines.forEach((line) => {
        line.remove();
      });
    }
  },
  methods: {
    /**
     * 递归绘制箭头
     */
    drawArrowLine() {
      this.drawLeaderLine(this.list);
    },
    /**
     * 根据上下级关系绘制线条
     */
    drawLeaderLine(list) {
      list.forEach((element) => {
        let start = document.getElementById(element.id);
        if (element.children && element.children.length) {
          element.children.forEach((child) => {
            let line = LeaderLine.setLine(
              start,
              document.getElementById(child.id)
            );
            line.color = "rgba(30, 130, 250, 0.5)";
            line.path = "grid";
            line.size = 3;
            line.setOptions({
              dash: { animation: true },
            });
            this.lines.push(line);
          });
          this.drawLeaderLine(element.children);
        }
      });
    },
    position_top(id, position) {
      let dom = document.getElementById(id);
      let height;
      if (dom) {
        height = dom.clientHeight;
      }
      let rt;
      if (position === "top") {
        rt = {
          height: height / 2 - 2 + "px",
          top: 0,
        };
      }
      if (position === "bottom") {
        rt = {
          height: height / 2 + 1 + "px",
          bottom: 0,
        };
      }
      return rt;
    },
    isFirst(id) {
      return (
        this.list.length > 1 && this.list.map((x) => x.id).indexOf(id) === 0
      );
    },
    isLast(id) {
      return (
        this.list.length > 1 &&
        this.list.map((x) => x.id).indexOf(id) === this.list.length - 1
      );
    },
  },
};
</script>

<style lang="scss" scoped>
.TreeRight {
  display: flex;
  .childs {
    .child {
      display: flex;
      background-color: #fff;
      .child-item {
        display: flex;
        align-items: center;
        margin: 10px 0;
        .childname {
          .content-box {
            text-align: left;
            border: 1px solid #e8e8e8;
            padding: 10px;
            height: 100px;
            border-radius: 2px;
            width: 100%;
            box-shadow: 0 2px 4px 0 rgba(181, 181, 181, 0.7);
          }
          cursor: pointer;
          height: 100%;
          display: flex;
          align-items: center;
          width: 220px;
          text-align: center;
          justify-content: center;
          position: relative;
          padding: 10px 0;
          .position-arrow {
            position: absolute;
            left: -22px;
          }
          .position-top {
            position: absolute;
            width: 3px;
            background-color: #fff;
            left: -23px;
            height: 10px;
          }
        }
        .childarrow {
          height: 100%;
          display: flex;
          align-items: center;
        }
      }
    }
    .child-children {
      display: flex;
      flex-direction: column;
      justify-content: center;
    }
  }
}
</style>
