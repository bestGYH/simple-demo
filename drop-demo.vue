<template>
  <div
    @dragenter="
      e => {
        onDragEnter(e);
      }
    "
    @dragover="handleDragover"
    @dragleave="onDragEnter('')"
  >
    <draggable
      v-for="(item, pIndex) in testData"
      :key="item.title"
      ghostClass="ghost"
      chosenClass="chosen"
      class="params-card"
      :class="{
        'drag-enter-dom': enterParamObj.enterParamKey === item.title,
        'is-last': enterParamObj.isLast
      }"
      @start="
        e => {
          onDragStart(e, pIndex);
        }
      "
      @end="
        e => {
          onDropEnd(e, pIndex);
        }
      "
      animation="1000"
    >
      <div
        :key="item.title"
        class="drag-item"
        :data-param-index="pIndex"
        :data-param-key="item.title"
      >
        <!-- 可拖拽标识 -->
        <yn-icon-svg type="drag-and-drop" class="drag-icon" />
        <span class="param-info">
          {{ item.title }}
          <!-- 标识最后一个元素 -->
          <i v-if="pIndex === testData.length - 1" class="last-dom-calss"></i>
        </span>
      </div>
    </draggable>
  </div>
</template>
<script>
import draggable from "vuedraggable";
const testData = [
  {
    title: "标题 1"
  },
  {
    title: "标题 2"
  },
  {
    title: "标题 3"
  },
  {
    title: "标题 4"
  }
];
export default {
  components: {
    draggable
  },
  data() {
    return {
      testData,
      enterParamObj: {}
    };
  },
  mounted() {},
  computed: {},
  methods: {
    // 开始拖拽
    onDragStart(e, index) {
      this.dragParamIndex = index;
    },
    onDragEnter(e) {
      if (e) {
        let { target } = e;
        this.enterParamObj = {};
        console.log("onDragenter-parentElement", target.parentElement);

        let enterParamIndex = target?.parentElement?.dataset?.paramIndex ?? "";
        let enterParamKey = target?.parentElement?.dataset?.paramKey ?? "";

        let isLast = false;
        if (
          target.className &&
          typeof target.className == "string" &&
          target.className.indexOf("last-dom-calss") > -1
        ) {
          //放入区域是在底部区域就放底下插入
          console.log(
            "target?.parentElement?.parentElement",
            target?.parentElement?.parentElement
          );
          isLast = true;
          enterParamIndex =
            target?.parentElement?.parentElement?.dataset?.paramIndex ?? "";
          enterParamKey =
            target?.parentElement?.parentElement?.dataset?.paramKey ?? "";
        }
        this.enterParamObj = {
          enterParamIndex,
          enterParamKey,
          isLast
        };
        console.log("onDragenter", e);

        console.log("onDragenter-parentElement", target.parentElement);
        console.log("onDragenter", isLast);
      }
    },
    onDropEnd(e) {
      const paramItem = this.testData[this.dragParamIndex];
      this.testData.splice(this.dragParamIndex, 1);
      let { enterParamIndex, isLast } = this.enterParamObj;
      enterParamIndex =
        this.dragParamIndex < enterParamIndex && !isLast
          ? enterParamIndex - 1
          : enterParamIndex;
      this.testData.splice(enterParamIndex, 0, paramItem);
      this.enterParamObj = {};
      console.log("onDropEnd", e);
    },
    handleDragover(event) {
      // for drop event
      event.preventDefault();
    }
  }
};
</script>
<style scoped lang="less">
.drag-item {
  display: flex;
  .param-info {
    position: relative;
    flex: 1;
    .last-dom-calss {
      position: absolute;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 40%;
      display: block;
      background: none;
    }
  }
}
.params-card {
  .drag-icon {
    visibility: hidden;
  }
  &:hover {
    cursor: move;
    .drag-icon {
      visibility: visible;
    }
  }
  &::before,
  &::after {
    pointer-events: none;
    display: block;
    content: "";
    height: 2px;
    background: @yn-primary-color;
    position: absolute;
    left: 0;
    width: 100%;
    opacity: 0;
  }
  &::before {
    top: -2px;
  }
  &::after {
    bottom: 2px;
  }
}
.drag-enter-dom {
  position: relative;
  &:not(.is-last)::before {
    opacity: 1;
  }
  &.is-last {
    &::after {
      opacity: 1;
    }
  }
}
.params-card .drag-item {
  background: @yn-background-color-light;
  &.unfill {
    background: @yn-background-color;
  }
  &.chosen {
    background-color: @yn-primary-2!important;
    opacity: 0.8 !important;
  }
  &.ghost {
    background-color: @yn-primary-2!important;
    opacity: 1 !important;
  }
}
</style>
