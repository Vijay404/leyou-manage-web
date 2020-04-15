<template>
  <v-list class="pt-0 pb-0" dense>
    <TreeItem
      class="item" :model="model" v-for="(model, index) in db" :key="index"
      :url="url"
      :isEdit="isEdit"
      :nodes="nodes"
      @handleAdd="handleAdd"
      @handleEdit="handleEdit"
      @handleDelete="handleDelete"
      @handleClick="handleClick"
    />
  </v-list>
</template>

<script>
  import TreeItem from './TreeItem';

  export default {
    name: "vTree",
    props: {
      url: String,
      isEdit: {
        type: Boolean,
        default: false
      },
      flag: {
        type: Boolean,
        default: false
      },
      treeData:{
        type:Array
      }
    },
    data() {
      return {
        db: [],
        nodes:{
          opened:null,
          selected:{isSelected:false}
        }
      }
    },
    components: {
      TreeItem
    },
    created() {
      if(this.treeData && this.treeData.length > 0){
        this.db = this.treeData;
        return;
      }
      this.getData();
    },
    methods: {
      getData() {
        this.$http.get(this.url, {params: {pid: 0}}).then(resp => {
          this.db = resp.data;
          this.db.forEach(n => n['path'] = [n.name])
        })
      },
      handleAdd(node) {
        // 修改标记
        this.flag = false;
        console.log(this.flag);
        this.$http({
          method: this.flag ? "put" : "post",
          url: "/item/category",
          data: this.$qs.stringify(node)
        }).then(() => {
          this.getData();
          this.$message.success("新增成功！");
        }).catch(() => {
          this.$message.error("新增失败！");
        });
        this.$emit("handleAdd", this.copyNodeInfo(node));
      },
      handleEdit(id,name) {
        // 发送请求更新分类，传递当前节点id和名称
        this.flag = true;
        this.$http({
          method: this.flag ? "put" : "post",
          url: "/item/category",
          data: {id,name}
        }).then(() => {
          this.$message.success("修改成功！");
        }).catch(() => {
          this.$message.error("修改失败！");
        });
        this.$emit("handleEdit", id, name)
      },
      handleDelete(id) {
        // 发送请求删除分类
        this.$http.delete("/item/category?id="+id)
          .then(() => {
            this.getData();
            this.$message.success("删除成功！");
          }).catch(() => {
          this.getData();
          this.$message.error("删除失败！");
        });
        this.deleteById(id, this.db);
        this.$emit("handleDelete", id);
      },
      handleClick(node){
        this.$emit("handleClick", this.copyNodeInfo(node))
      },
      // 根据id删除
      deleteById(id, arr) {
        let src = arr || this.db;
        for (let i in src) {
          let d = src[i];
          if (d.id === id) {
            src.splice(i, 1)
            return;
          }
          if (d.children && d.children.length > 0) {
            this.deleteById(id, d.children)
          }
        }
      },
      copyNodeInfo(node){
        const o = {};
        for(let i in node){
          o[i] = node[i];
        }
        return o;
      }
    },
    watch: {}
  }
</script>

<style scoped>
  .item {
    cursor: pointer;
  }
</style>
