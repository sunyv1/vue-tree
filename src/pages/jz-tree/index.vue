
<template>
  <div class="jz-tree_content_wrap" v-if="data.length>0">
    <ul class="jz-tree">
      <tree-item
        v-for="(item,index) in data"
        :key="index"
        :node.sync="item"
        :num.sync="index"
        root="0"
        :nodes.sync="data.length"
        :ischeck="isCheck"
        :callback="func"
        :expandfunc="expand"
        :cxtmenufunc="contextmenu"
        :trees.sync="data"
        :selectfunc="selectfunc"
      />
    </ul>
  </div>
</template>
<script>
import Vue from "vue";
import treeItem from "./treeItem";

export default {
  components: {
    treeItem
  },
  data() {
    return {
      data: []
    };
  },
  props: {
    // 树数据
    list: {
      type: Array,
      twoWay: true
    },
    // 点击节点回调
    func: {
      type: Function,
      default: null
    },
    // 点击展开回调
    expand: {
      type: Function,
      default: null
    },
    // 右击事件
    contextmenu: {
      type: Function,
      default: function() {}
    },
    // 是否展开
    isOpen: {
      type: Boolean,
      twoWay: true,
      default: false
    },
    // 是否选中
    isCheck: {
      type: Boolean,
      twoWay: true,
      default: false
    },
    select: {
      type: Function,
      default: null
    }
  },
  watch: {
    list: {
      handler: function() {
        this.initTreeData();
      },
      deep: true
    }
  },
  methods: {
    // 获取所有被勾选的节点
    selectfunc(node) {
      let arr = [];
      const func = data => {
        data.forEach(x => {
          if (x.ckbool == true) {
            arr.push(x);
          }
          if (x.children) {
            func(x.children);
          }
        });
      };
      func(this.data);
      this.select(arr);
    },
    // 初始化数据
    initTreeData() {
      var tempList = JSON.parse(JSON.stringify(this.list));
      // 递归操作，增加删除一些属性。比如: 展开/收起
      var recurrenceFunc = data => {
        data.forEach(m => {
          if (!m.hasOwnProperty("clickNode")) {
            m.clickNode = m.hasOwnProperty("clickNode") ? m.clickNode : false;
          }

          if (!m.hasOwnProperty("ckbool")) {
            m.ckbool = m.hasOwnProperty("ckbool") ? m.ckbool : false;
          }

          if (!m.hasOwnProperty("isCheck")) {
            m.isCheck = m.hasOwnProperty("isCheck") ? m.isCheck : this.isCheck;
          }

          m.children = m.children || [];

          m.hover = false;

          if (!m.hasOwnProperty("isFolder")) {
            m.isFolder = m.hasOwnProperty("open") ? m.open : this.isOpen;
          }

          if (!m.hasOwnProperty("isExpand")) {
            m.isExpand = m.hasOwnProperty("open") ? m.open : this.isOpen;
          }

          m.loadNode = 0;

          recurrenceFunc(m.children);
        });
      };
      recurrenceFunc(tempList);
      this.data = tempList;
    }
  },

  update() {
    this.initTreeData();
  },
  mounted() {
    Vue.nextTick(() => {
      this.initTreeData();
    });
  }
};
</script>

<style lang="less">
.jz-tree_content_wrap {
  height: 100%;
  user-select: none;
  div {
    &.left {
      float: left;
      width: 100%;
    }
  }
}

.expendIcon {
  background-position: -74px -36px;
  line-height: 0;
  margin: 0;
  width: 16px;
  height: 16px;
  display: inline-block;
  vertical-align: middle;
  border: 0 none;
  cursor: pointer;
  outline: none;
  position: absolute;
  top: 4px;
  background-color: transparent;
  background-repeat: no-repeat;
  background-attachment: scroll;
}

.jz-tree {
  width: 100%;
  height: auto;
  // overflow-y: scroll;
  overflow-x: auto;
  margin: 0;
  padding: 5px;
  color: #273037;
  font-size: 14px;

  li {
    position: relative;
    padding: 0;
    margin: 0;
    list-style: none;
    line-height: 24px;
    text-align: left;
    white-space: nowrap;
    outline: 0;
    ul {
      margin: 0;
      padding: 0 0 0 18px;
      &.line {
        background: url("./images/line_spot.gif") 0 0 repeat-y;
      }
    }
    a {
      padding: 1px 3px 0 5px;
      margin: 0;
      cursor: pointer;
      color: #273037;
      height: 18px;
      background-color: transparent;
      text-decoration: none;
      vertical-align: top;
      display: inline-block;
      &:hover {
        text-decoration: none;
        color: #527286;
      }
      & > span {
        &.curSelectedNode {
          padding-top: 0px;
          height: 18px;
          opacity: 0.9;
          padding: 2px 4px;
          background: #2d8cf0;
          color: #000;
          margin-left: -4px;
        }
      }
      &.curSelectedNode_Edit {
        padding-top: 0px;
        background-color: #ffe6b0;
        color: black;
        height: 16px;
        border: 1px #ffb951 solid;
        opacity: 0.8;
      }
      &.tmpTargetNode_inner {
        padding-top: 0px;
        background-color: #316ac5;
        color: white;
        height: 16px;
        border: 1px #316ac5 solid;
        opacity: 0.8;
        filter: alpha(opacity=80);
      }
      input {
        &.rename {
          height: 14px;
          width: 80px;
          padding: 0;
          margin: 0;
          font-size: 12px;
          border: 1px #7ec4cc solid;
          *border: 0px;
        }
      }
    }
    span {
      line-height: 16px;
      margin-right: 2px;
      top: 3px;
      display: inline-block;
      &.button {
        line-height: 0;
        margin: 0;
        width: 16px;
        height: 16px;
        display: inline-block;
        vertical-align: middle;
        border: 0 none;
        cursor: pointer;
        outline: none;
        background-color: transparent;
        background-repeat: no-repeat;
        background-attachment: scroll;
        background-image: url("./images/all.png");

        &.chk {
          width: 13px;
          height: 13px;
          margin: 0 3px 0 0;
          cursor: pointer;
          &.checkbox_false_full {
            background-position: 0 0;
          }
          &.checkbox_false_full_focus {
            background-position: 0 -14px;
          }
          &.checkbox_false_part {
            background-position: 0 -28px;
          }
          &.checkbox_false_part_focus {
            background-position: 0 -42px;
          }
          &.checkbox_false_disable {
            background-position: 0 -56px;
          }
          &.checkbox_true_full {
            background-position: -14px 0;
          }
          &.checkbox_true_full_focus {
            background-position: -14px -14px;
          }
          &.checkbox_true_part {
            background-position: -14px -28px;
          }
          &.checkbox_true_part_focus {
            background-position: -14px -42px;
          }
          &.checkbox_true_disable {
            background-position: -14px -56px;
          }
          &.radio_false_full {
            background-position: -28px 0;
          }
          &.radio_false_full_focus {
            background-position: -28px -14px;
          }
          &.radio_false_part {
            background-position: -28px -28px;
          }
          &.radio_false_part_focus {
            background-position: -28px -42px;
          }
          &.radio_false_disable {
            background-position: -28px -56px;
          }
          &.radio_true_full {
            background-position: -42px 0;
          }
          &.radio_true_full_focus {
            background-position: -42px -14px;
          }
          &.radio_true_part {
            background-position: -42px -28px;
          }
          &.radio_true_part_focus {
            background-position: -42px -42px;
          }
          &.radio_true_disable {
            background-position: -42px -56px;
          }
        }
        &.switch {
          width: 18px;
          height: 18px;
        }
        &.root_open {
          // background-position: -92px -54px;
          background: url("./images/root_open.png");
          background-size: 100% 100%;
        }
        &.root_close {
          // background-position: -74px -54px;
          background: url("./images/root_close.png");
          background-size: 100% 100%;
        }
        &.roots_open {
          background-position: -92px 0;
        }
        &.roots_close {
          background-position: -74px 0;
        }
        &.center_open {
          background: url("./images/center_open.png");
          background-size: 100% 100%;
        }
        &.center_close {
          background: url("./images/center_close.png");
          background-size: 100% 100%;
        }
        &.bottom_open {
          background: url("./images/bottom_open.png");
          background-size: 100% 100%;
        }
        &.bottom_close {
          background: url("./images/bottom_close.png");
          background-size: 100% 100%;
        }
        &.noline_open {
          background-position: -92px -72px;
        }
        &.noline_close {
          background-position: -74px -72px;
        }
        &.root_docu {
          background: none;
        }
        &.roots_docu {
          background-position: -56px 0;
        }
        &.center_docu {
          background-position: -56px -18px;
        }
        &.bottom_docu {
          background-position: -56px -36px;
        }
        &.noline_docu {
          background: none;
        }
        &.ico_open {
          margin-right: 2px;
          background-position: -110px -16px;
          vertical-align: top;
          *vertical-align: middle;
        }
        &.ico_close {
          margin-right: 2px;
          background-position: -110px 0;
          vertical-align: top;
          *vertical-align: middle;
        }
        &.ico_docu {
          margin-right: 2px;
          background-position: -110px -32px;
          vertical-align: top;
          *vertical-align: middle;
        }
        &.add {
          margin: -2px 2px 0 0;
          // background-image: url("../../../static/img/tree/red.png");
          background-size: 100% 100%;
          vertical-align: top;
          vertical-align: middle;
        }
        &.edit {
          margin: -2px 2px 0 0;
          background-position: -110px -48px;
          vertical-align: top;
          *vertical-align: middle;
        }
        &.remove {
          margin: 4px 2px 0 0;
          // background-image: url("../../../static/img/tree/go.png");
          background-size: 100% 100%;
          vertical-align: top;
          *vertical-align: middle;
        }
        &.up {
          margin: 4px 2px 0 0;
          // background-image: url("../../../static/img/tree/info.png");
          background-size: 100% 100%;
          vertical-align: top;
          *vertical-align: middle;
        }
        &.down {
          background: url("./images/down.png");
        }
      }
    }
  }
}
.zTreeMask {
  z-index: 10000;
  background-color: #cfcfcf;
  opacity: 0;
  filter: alpha(opacity=0);
  position: absolute;
}
.loadSyncNode {
  width: 16px;
  height: 16px;
  position: relative;
  display: inline-block;
  background-image: url("./images/load.gif");
}
// .node_row {
//   position: absolute;
//   left: -100px;
//   right: 0px;
//   z-index: -1;
//   height: 20px;
//   background: #ecf6ff;
// }
.node_icon {
  width: 14px;
  height: 14px;
}
.node_name {
  display: inline-block;
}
</style>


