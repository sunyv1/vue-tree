### 因为很多项目都会用到树形组件。所以自己做了一个树形组件，现在和大家分享一下
源码太多就不贴上来了，下面是链接：
#### 使用方法
```
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
```
#### 在需要使用的页面引入zj-tree
| 参数 | 值 | 作用 |
|--|--|--|
| isOpen | Boolean | 是否展开 |
| list | Array | 展示的数据 |
| func | Function | 点击节点的回调（返回值是点击的节点） |
| expand | Function | 展开节点的回调（返回值是展开的节点） |
| contextmenu | Function | 右击节点的回调（返回值是右击的节点） |
| isCheck | Boolean | 是否开启选框 |
| select | Function | 开启选框的时候点击选框的回调（返回值是所有勾选的节点数组） |
#### 鼠标经过时候回显示选项，点击增加选项会弹出输入框
#### 其它需要定制的功能就自己修改源码吧

