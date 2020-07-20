<template>
  <div>
    <div>
    <el-collapse v-model="activeNames" @change="handleChange">

      <el-collapse-item v-for="(article,index) in articles" :title="article.title" :name="article.id">
        <hr>
        作者:<span v-text="article.author"></span>
        <hr>
        内容:<br>
        <span v-text="article.content"></span>

      </el-collapse-item>
    </el-collapse>
    </div>
    <div class="page" v-if="total >= params.pageSize">
      <el-pagination
        layout="prev, pager, next"
        @current-change='handleCurrentChange'
        :page-size='params.pageSize'
        :total="total">
      </el-pagination>
    </div>
  </div>
</template>
<script>
  import axios from '@/http/axios'

  export default {
    data() {
      return {
        activeNames: [],
        articles:[],
        total: 0,
        params: {
          page: 1,
          pageSize: 6,
        },
        loading: false,
      }
    },
    methods: {
      handleChange(val) {
        console.log(val);
        axios.post('/manager/article/updateViewCount', {id: val.toString()}).then(function (response) {
          console.log(response);
          // console.log(this.activeNames);
        });
      },
      handleCurrentChange(page) {
        this.params.page = page;
      },
      findAllArticles() {
        this.loading = true;
        let url = '/manager/article/findArticlePage';
        axios.get(url, {
          params: this.params
        })
          .then(({data: result}) => {
            // 将查询到的数据绑定到模型中
            console.log(result);
            // this.total = result.data.total;
            // this.articles = result.data.list;
            this.total = result.total;
            this.articles = result.list;
          }).catch((error) => {
          this.$notify.error({
            title: '错误',
            message: '网络异常'
          })
        }).finally(() => {
          this.loading = false;
        });
      },
    },
    created() {
      this.findAllArticles();
    }
  }
</script>



