<template>
  <div id="app">
    <jz-tree
      :isOpen="true"
      :isCheck="true"
      :list="list"
      :func="onClick"
      :expand="openClick"
      :contextmenu="rightClick"
      :select="selectClick"
    />
  </div>
</template>

<script>
import jzTree from "@/pages/jz-tree/index.vue";
export default {
  components: {
    jzTree
  },
  data() {
    return {
      list: [
        {
          name: "音乐",
          id: 1,
          children: [
            {
              name: "流行",
              id: 2
            },
            {
              name: "伤感",
              id: 3
            }
          ]
        },
        {
          name: "视频",
          id: 4,
          children: [
            {
              name: "热血",
              id: 5
            },
            {
              name: "惊悚",
              id: 6
            }
          ]
        }
      ]
    };
  },
  methods: {
    // 单击节点的回调
    onClick(node) {
      console.log("单击节点的回调", node);
    },
    // 点击展开的回调
    openClick(node) {
      console.log("点击展开", node);
    },
    // 右击回调
    rightClick(node) {
      console.log("右击节点", node);
    },
    // 点击选框回调
    selectClick(nodes) {
      console.log("点击选框", nodes);
    }
  }
};
</script>

<style lang="less">
#app {
  display: flex;
  justify-content: left;
}
</style>
