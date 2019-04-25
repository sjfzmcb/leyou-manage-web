<template>
  <v-card>
    <v-card-title>
      <v-btn color="primary">新增品牌</v-btn>
      <!--空间隔离组件-->
      <v-spacer/>
      <!--搜索框，与search属性关联-->
      <v-text-field label="输入关键字搜索" v-model="keyword" append-icon="search" hide-details />
    </v-card-title>

    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading" class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center"><img v-if="props.item.image" :src="props.item.image" width="130" height="40"/></td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="text-xs-center">
          <v-icon
            small
            class="mr-2"
            @click="editItem(props.item)"
          >
            edit
          </v-icon>
          <v-icon
            small
            @click="deleteItem(props.item)"
          >
            delete
          </v-icon>
        </td>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
    export default {
        name: "MyBrand",
        data(){
          return{
            headers:[  //头信息
              {text: '品牌ID',align: 'center',sortable: true,value: 'id'},
              {text: '品牌名称',align: 'center',sortable: false,value: 'name'},
              {text: '品牌LOGO',align: 'center',sortable: false,value: 'image'},
              {text: '品牌首字母',align: 'center',sortable: true,value: 'letter'},
              {text: '操作',align: 'center',sortable: false}
            ],
            brands:[],  //当前页品牌数据
            pagination:{},  //分页信息
            totalBrands:0, //总条数
            loading:false, //是否在加载中
            keyword:""  //搜索条件
          }
        },
      created() {

        //去后台查询
        this.loadBrands();
      },
      watch:{
        keyword(){
            this.pagination.page = 1;
        },
        pagination:{  //监视pagination属性得变化
          deep:true,  //deep为true,会监视pagination的属性及属性中的对象属性变化
          handler(){
            //变化后的回调函数，这里我们再次调用loadBrands即可
            this.loadBrands();
          }
        }
      },
      methods:{
          loadBrands(){
            this.loading = true;
            this.$http.get("item/brand/page",{
              params:{
                //搜索条件
                key:this.keyword,   //搜索关键字
                page:this.pagination.page,   //当前页
                rows:this.pagination.rowsPerPage,  //每页大小
                sortBy:this.pagination.sortBy,  //排序方式
                desc:this.pagination.descending  //排序方式
              }
            })
              .then(resp => {
                  this.brands = resp.data.items;
                  this.totalBrands = resp.data.total;
                  this.loading = false;
              })
              .catch(function (error) {

              });
          }
      }
    }
</script>

<style scoped>

</style>
