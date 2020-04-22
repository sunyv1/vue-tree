<template>
  <li :class="liClassVal">
    <!-- 显示线框 -->
    <span :class="spanClassVal" @click="open(node)"></span>
    <a
      class="a-node"
      @mouseenter="enterFunc(node)"
      @mouseleave="leaveFunc(node)"
      @contextmenu.prevent="cxtmenufunc(node)"
    >
      <span :class="{loadSyncNode:node.loadNode==1}" v-if="node.loadNode==1"></span>
      <span :class="node.iconClass" v-show="node.iconClass" v-else></span>
      <!-- 复选框 -->
      <span
        v-show="ischeck"
        id="treeDemo_5_check"
        class="button chk"
        :class="{'checkbox_false_full':!node.ckbool,'checkbox_true_full':node.ckbool}"
        @click="ckFunc(node)"
        treenode_check
      ></span>
      <!-- 结点名称 -->
      <img v-if="node.icon" class="node_icon" :src="node.icon" />
      <!-- <div class="node_row" :class="activeClass"></div> -->
      <span class="node_name" :class="activeClass" @click="Func(node)">{{node.name}}</span>
      <span v-if="node.hover" class="hover-button">
        <img style="width:16px" src="./images/加.png" @click="isShowInput=true" />
        <img style="width:16px" src="./images/减.png" />
      </span>
      <div class="add-node" v-if="isShowInput">
        <div class="add-node-triangle"></div>
        <div class="add-node-input">
          <span>请输入：</span>
          <input type="text" />
          <button>确定</button>
          <button @click="isShowInput=false">取消</button>
        </div>
      </div>
    </a>
    <ul :class="ulClassVal" v-show="node.isFolder">
      <tree-item
        v-for="(item,index) in node.children"
        :key="index"
        :callback="callback"
        :expandfunc="expandfunc"
        :cxtmenufunc="cxtmenufunc"
        :node.sync="item"
        :num.sync="index"
        root="1"
        :nodes.sync="node.children.length"
        :ischeck="ischeck"
        :trees.sync="trees"
        :selectfunc="selectfunc"
      />
    </ul>
  </li>
</template>
<script>
export default {
  name: "treeItem",
  data() {
    return {
      parentNodenode: null,
      isShowInput: false //是否显示输入框
    };
  },
  props: {
    node: {
      type: Object,
      twoWay: true
    },
    num: {
      type: Number,
      twoWay: true
    },
    nodes: {
      type: Number,
      twoWay: true,
      default: 0
    },
    trees: {
      type: Array,
      twoWay: true,
      default: []
    },
    root: {
      type: String,
      twoWay: true
    },
    callback: {
      type: Function
    },
    expandfunc: {
      type: Function
    },
    selectfunc: {
      type: Function
    },
    cxtmenufunc: {
      type: Function
    },
    ischeck: {
      type: Boolean,
      twoWay: true,
      default: false
    }
  },
  methods: {
    // 单击节点
    Func(m) {
      // 查找点击的子节点
      var recurFunc = (data, list) => {
        data.forEach(i => {
          if (i.id == m.id) {
            i.clickNode = !i.clickNode;
            if (typeof this.callback == "function") {
              this.callback.call(null, m, list, this.trees);
            }
          } else {
            i.clickNode = false;
          }
          if (i.children) {
            recurFunc(i.children, i);
          }
        });
      };
      recurFunc(this.trees, this.trees);
    },
    // 点击选框
    ckFunc(m) {
      m.ckbool = !m.ckbool;
      // 查找复选框的所有子节点
      var recurFuncChild = data => {
        data.forEach(i => {
          i.ckbool = m.ckbool;
          if (i.children) recurFuncChild(i.children);
        });
      };
      recurFuncChild(m.children);

      // 查找复选框的所有父节点
      var parentId = m.parentId;
      var recurFuncParent = (data, list) => {
        data.forEach(i => {
          if (i.id == parentId && parentId > 0) {
            parentId = i.parentId;
            // 判断父亲节点的儿子节点选中个数
            var childrenCks = i.children.filter(child => {
              return child.ckbool == true;
            });
            // 如果大于0, 证明不能取消选中状态
            i.ckbool = childrenCks.length > 0;
            // 递归查找
            recurFuncParent(this.trees, i);
          } else if (i.id == m.id && i.parentId == 0) {
            i.ckbool = m.ckbool;
            isFindRootBool = true;
          } else {
            recurFuncParent(i.children, i);
          }
        });
      };
      recurFuncParent(this.trees, this.trees);

      this.selectfunc(m);
    },
    // 展开节点
    open(m) {
      m.isExpand = !m.isExpand;
      if (typeof this.expandfunc == "function") {
        // 收起
        if (m.loadNode != 2) {
          this.expandfunc.call(null, m);
          m.isFolder = !m.isFolder;
        } else {
          m.isFolder = !m.isFolder;
        }
      } else {
        m.isFolder = !m.isFolder;
      }
    },
    // 经过节点
    enterFunc(m) {
      m.hover = true;
      console.log("进来了", m);
    },
    // 离开节点
    leaveFunc(m) {
      m.hover = false;
      console.log("离开了", m);
    }
  },
  computed: {
    // 给（根 和 子树）赋值不同的样式
    rootClass() {
      var strRootClass = "";

      // 根判断
      if (this.root == "0") {
        this.node.children = this.node.children || [];
        strRootClass =
          this.num == 0 && this.node.children.length == 0
            ? "roots_docu"
            : this.nodes == 1 || (this.num == 0 && this.nodes != this.num + 1)
            ? "root_"
            : this.nodes == this.num + 1
            ? "bottom_"
            : "center_";

        // 子树判断
      } else if (this.root == "1") {
        this.node.children = this.node.children || [];
        strRootClass =
          this.nodes > 1 &&
          this.node.children.length > 0 &&
          this.nodes != this.num + 1
            ? "center_"
            : (this.num == 0 && this.nodes > 1) || this.nodes != this.num + 1
            ? "center_docu"
            : (this.nodes == 1 && this.num != 0) ||
              (this.nodes == this.num + 1 && this.node.children.length > 0)
            ? "bottom_"
            : "bottom_docu";
      }

      return strRootClass;
    },
    // 是否有儿子节点
    isChildren() {
      return this.num + 1 != this.nodes;
    },
    // 展开/收起
    prefixClass() {
      var returnChar = "";
      if (this.rootClass.indexOf("docu") == -1) {
        if (this.node.isFolder) {
          returnChar = "open";
        } else {
          returnChar = "close";
        }
      }

      if (
        this.node.children.length == 0 &&
        this.rootClass.indexOf("docu") == -1
      ) {
        returnChar = "docu";
      }

      return returnChar;
    },
    liClassVal() {
      return "level" + this.num;
    },
    spanClassVal() {
      return (
        "button level" +
        this.num +
        " switch " +
        this.rootClass +
        this.prefixClass
      );
    },
    // 激活状态的样式
    activeClass() {
      return this.node.clickNode
        ? "level" + this.num + " curSelectedNode"
        : "level" + this.num;
    },
    ulClassVal() {
      return this.isChildren && this.node.children.length > 0
        ? "level" + this.num + " line"
        : "level" + this.num;
    }
  }
};
</script>


<style lang="less">
.hover-button {
  display: inline-block;
  vertical-align: middle;
  z-index: 10;
}
.add-node {
  position: fixed;
  z-index: 15;
  .add-node-triangle {
    width: 0;
    height: 0;
    border-style: solid;
    border-width: 10px;
    border-color: transparent transparent #ccc transparent;
    margin-left: 10px;
    margin-top: -15px;
  }
  .add-node-input {
    background-color: #ccc;
    height: 30px;
    display: flex;
    align-items: center;
    padding-left: 5px;
    padding-right: 5px;
  }
}
</style>