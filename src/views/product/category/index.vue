<!--  -->
<template>
  <el-tree
    :data="menus"
    :props="defaultProps"
    @node-click="handleNodeClick"
  ></el-tree>
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
      menus: [],
      defaultProps: {
        children: "children",
        label: "name",
      },
    };
  },
  //方法集合
  methods: {
    handleNodeClick(data) {
      console.log(data);
    },
    getMenus() {
      return request({
        url: "dev-api/gulimallproduct/category/list/tree",  //http://localhost:88/dev-api/product/category/list/tree
        method: "get",
        baseURL: "http://172.28.65.252:88"   //"localhost:88/dev-api"//172.28.65.252
      }).then(({ data }) => {
        console.log(data);
        this.menus=data
      });
    },
  },
  //监听属性 类似于data概念
  computed: {},
  //监控data中的数据变化
  watch: {},

  //生命周期 - 创建完成（可以访问当前this实例）
  created() {
    console.log("created");
    this.getMenus();
  },
  //生命周期 - 挂载完成（可以访问DOM元素）
  mounted() {},
  beforeCreate() {
    console.log("beforeCreate");
  }, //生命周期 - 创建之前
  beforeMount() {}, //生命周期 - 挂载之前
  beforeUpdate() {}, //生命周期 - 更新之前
  updated() {}, //生命周期 - 更新之后
  beforeDestroy() {
    console.log("beforeDestroy");
  }, //生命周期 - 销毁之前
  destroyed() {}, //生命周期 - 销毁完成
  activated() {
    console.log("activated");
  }, //如果页面有keep-alive缓存功能，这个函数会触发
};
</script>
<style scoped>
</style>