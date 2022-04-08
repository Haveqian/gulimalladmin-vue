<!-- default-expand-all : 是否默认展开所有 -->
<template>
  <div>
    <el-tree
      :data="menus"
      :props="defaultProps"
      @node-click="handleNodeClick"
      show-checkbox
      node-key="catId"
      :expand-on-click-node="false"
      :default-expanded-keys="expandedkeys"
    >
      <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button
            v-if="data.catLevel <= 2"
            type="text"
            size="mini"
            @click="() => append(data)"
          >
            Append
          </el-button>
          <el-button
            v-if="data.children.length == 0"
            type="text"
            size="mini"
            @click="() => remove(node, data)"
          >
            Delete
          </el-button>
          <el-button type="text" size="mini" @click="() => edit(data)">
            edit
          </el-button>
        </span>
      </span>
      ></el-tree
    >

    <el-dialog
      :title="fromtitle"
      :visible.sync="dialogVisible"
      width="500px"
      :close-on-click-modal="false"
      :close-on-press-escape="false"
    >
      <el-form :model="category">
        <el-form-item label="菜单名">
          <el-input v-model="category.name"></el-input>
        </el-form-item>
        <el-form-item label="图标位置">
          <el-input v-model="category.icon"></el-input>
        </el-form-item>
        <el-form-item label="计量单位">
          <el-input v-model="category.productUnit"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submit()">确 定</el-button>
        <el-button @click="resetForm()">重置</el-button>
        <el-button @click="dialogVisible = false">取 消</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
//这里可以导入其他文件（比如：组件，工具js，第三方插件js，json文件，图片文件等等）
//例如：import 《组件名称》 from '《组件路径》';

import request from "@/utils/request";
export default {
  //import引入的组件需要注入到对象中才能使用
  components: {},
  data() {
    return {
      fromtitle: "",
      tip: "",
      flag: "",
      category: {
        catId: 0,
        name: "",
        parentCid: 0,
        catLevel: 0,
        showStatus: 1,
        sort: 0,
        icon: "",
        productUnit: "",
      },
      dialogVisible: false,
      expandedkeys: [],
      menus: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  //方法集合
  methods: {
    //点击这个节点的操作
    handleNodeClick(data) {
      console.log(data);
    },
    //得到全部商品
    getMenus() {
      return request({
        url: "dev-api/gulimallproduct/category/list/tree", //http://localhost:88/dev-api/product/category/list/tree
        method: "get",
        baseURL: "http://172.28.65.252:88", //"localhost:88/dev-api"//172.28.65.252
      }).then(({ data }) => {
        console.log("当前商品", data);
        this.menus = data;
      });
    },
    submit(){
      if(this.flag == "updata"){
        console.log("updata")
        this.updata().then(()=>{
          this.$message({
          message: `${this.fromtitle}成功`,
          type: 'success'
        });
        })
      }
      if(this.flag == "addCategory"){
        console.log("addCategory")
        this.addCategory().then(()=>{
          this.$message({
          message: `${this.fromtitle}成功`,
          type: 'success'
        });
        })
      }
      console.log("submit")
    },
    //修改
    edit(data) {
      console.log("a", data);
      this.fromtitle = "修改商品"
      this.flag = "updata";
      this.dialogVisible = true;
      this.category.name = data.name;
      this.category.catId = data.catId;
      this.category.icon = data.icon;
      this.category.productUnit = data.productUnit;
    },

    updata() {
      var {catId,name,icon,productUnit} = this.category;
      return request({
        url: "dev-api/gulimallproduct/category/update", //http://localhost:88/dev-api/product/category/list/tree
        method: "post",
        data: {catId,name,icon,productUnit},
        baseURL: "http://172.28.65.252:88", //"localhost:88/dev-api"//172.28.65.252
      }).then(() => {
        console.log("修改菜单", this.category);
        this.tip = "修改成功";
        alert;
        this.dialogVisible = false;
        this.expandedkeys = [this.category.parentCid];
        this.getMenus();
      });
    },

    //添加
    addCategory() {
      return request({
        url: "dev-api/gulimallproduct/category/save", //http://localhost:88/dev-api/product/category/list/tree
        method: "post",
        data: this.category,
        baseURL: "http://172.28.65.252:88", //"localhost:88/dev-api"//172.28.65.252
      }).then(() => {
        console.log("添加菜单", this.category);
        this.tip = "添加成功";
        alert;
        this.dialogVisible = false;
        this.expandedkeys = [this.category.parentCid];
        this.getMenus();
        this.category.name = "";
      });
    },
    resetForm() {
      this.$refs.resetFields();
    },
    append(data) {
      this.fromtitle = "添加商品";
      this.flag = "addCategory";
      this.category.name = "";
      this.category.productUnit = "";
      this.category.icon = "";
      console.log("添加数据", data);
      this.dialogVisible = true;
      this.category.parentCid = data.catId;
      this.category.catLevel = data.catLevel * 1 + 1;
    },
    //移除
    remove(node, data) {
      this.$confirm(`是否删除【${data.name}】商品`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning",
      })
        .then(() => {
          this.expandedkeys = [data.parentCid];
          var ids = [data.catId];
          return request({
            url: "dev-api/gulimallproduct/category/delete",
            method: "post",
            data: ids,
            baseURL: "http://172.28.65.252:88",
          }).then(({ data }) => {
            this.$message({
              type: "success",
              message: "删除成功!",
            });
            console.log("删除成功" + ids);
            this.getMenus();
          });
        })
        .catch(() => {});
    },
  },
  //监听属性 类似于data概念
  computed: {},
  //监控data中的数据变化
  watch: {},

  //生命周期 - 创建完成（可以访问当前this实例）
  created() {
    this.getMenus();
  },
  //生命周期 - 挂载完成（可以访问DOM元素）
  mounted() {},
  beforeCreate() {}, //生命周期 - 创建之前
  beforeMount() {}, //生命周期 - 挂载之前
  beforeUpdate() {}, //生命周期 - 更新之前
  updated() {}, //生命周期 - 更新之后
  beforeDestroy() {}, //生命周期 - 销毁之前
  destroyed() {}, //生命周期 - 销毁完成
  activated() {}, //如果页面有keep-alive缓存功能，这个函数会触发
};
</script>
<style scoped>
</style>