<template>
    <div>
      <v-layout class="px-2 pb-2">
        <v-flex xs="2">
          <v-btn depressed color="primary">新增品牌</v-btn>
        </v-flex>
        <v-flex xs="4">
          <v-text-field hide-details label="搜索" v-model="key"></v-text-field>
        </v-flex>
      </v-layout>
      <v-data-table
        :headers="headers"
        :items="brands"
        :pagination.sync="pagination"
        :total-items="totalBrands"
        :loading="loading"
        class="elevation-1"
      >
        <template slot="items" slot-scope="props"> <!-- 每一行数据的渲染模板 -->
          <td class="text-xs-center">{{props.item.id}}</td>
          <td class="text-xs-center">{{props.item.name}}</td>
          <td class="text-xs-center"><img :src="props.item.image"/></td>
          <td class="text-xs-center">{{props.item.letter}}</td>
          <td class="text-xs-center">
            <v-btn flat icon color="info">
              <v-icon>edit</v-icon>
            </v-btn>
            <v-btn flat icon color="error">
              <v-icon>delete</v-icon>
            </v-btn>
          </td>
        </template>
      </v-data-table>
    </div>
</template>

<script>
    export default {
      name: "MyBrand",
      data(){
        return{
          headers: [ //表头
            {text: 'id', value: 'id', align: 'center', sortable: true},
            {text: '名称', value: 'name', align: 'center', sortable: false},
            {text: 'LOGO', value: 'image', align: 'center', sortable: false},
            {text: '首字母', value: 'letter', align: 'center', sortable: true},
            {text: '操作', align: 'center', sortable: false},
          ],
          brands: [],//数据
          pagination: {}, // 分页对象
          totalBrands: 0, //数据总条数
          loading: false, //是否显示加载进度条
          key: "", //查询的字符串
        }
      },
      created(){
        // this.$http.get('/brand/page',{
        //   params:{
        //     page: 1,
        //   }
        // }).then(resp => {});
        // // 去后台查询品牌信息

        this.brands = [
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
          {id: 1,name: "OPPO", image:"1.jpg", letter:"O"},
        ];
        this.totalBrands = 15;
        this.loadBrands();
      },
      watch:{
        key(){
          this.pagination.page = 1;
          this.loadBrands();
        },
        pagination:{
          deep:true,
          handler(){
            this.loadBrands();
          }
        }
      },
      methods:{
        loadBrands(){
          this.loading = true;
          //发送异步请求
          this.$http.get('/item/brand/page',{
            params:{
              page: this.pagination.page,
              rows: this.pagination.rowsPerPage,
              sortBy: this.pagination.sortBy,
              desc: this.pagination.descending,
              key: this.key//搜索条件
            }
          }).then(resp => {
            this.brands = resp.data.items;
            this.totalBrands = resp.data.total;
            this.loading = false;
          }).catch(resp => {
            this.loading = false;
            this.brands = {};
          });
        }
      }
    }
</script>

<style scoped>

</style>
