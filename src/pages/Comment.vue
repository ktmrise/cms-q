<!-- 评论管理 -->
<template>
  <div class="comment">
    <!-- 按钮区 -->
    <div class="btns">
      <el-input
        style="width:130px"
        size="small"
        placeholder="请输入关键字"
        v-model="params.keyWords"
        clearable>
      </el-input>

      <!--			<el-button size='small'>批量审核</el-button>-->
      <el-button size='small' @click="batchDeleteComment">批量删除</el-button>
      <el-button size='small' @click='toAddComment'>评论文章</el-button>
    </div>
    <!-- 按钮区结束 -->
    <!-- 数据区 -->
    <!-- {{multipleSelection}} -->
    <div class="comment_tbl" v-loading='loading'>
      <el-table :border='true' size='small' :data="comments" style="width: 100%"
                @selection-change="handleSelectionChange">
        <el-table-column type="selection" width="55" fixed></el-table-column>
        <el-table-column prop="content" label="评论内容"></el-table-column>
        <el-table-column prop="articleName" label="评论的文章"></el-table-column>
<!--        <el-table-column prop="status" label="状态" width="100" align='center'></el-table-column>-->
        <el-table-column prop="createTime" label="评论时间" width="180" align='center'></el-table-column>
        <el-table-column label="操作" width='80' fixed="right" align='center'>
          <template slot-scope='{row}'>
            <i class="fa fa-trash" @click='deleteComment(row.id)'></i>
<!--            <i class="fa fa-pencil" @click='toUpdateComment(row)'></i>-->
          </template>
        </el-table-column>
      </el-table>
    </div>
    <!-- 数据区结束 -->

    <!-- 分页 -->
    <div class="page" v-if="total >= params.pageSize">
      <el-pagination
        layout="prev, pager, next"
        @current-change='handleCurrentChange'
        :page-size='params.pageSize'
        :total="total">
      </el-pagination>
    </div>
    <!-- 分页结束 -->
    <!-- 模态框 -->
    <el-dialog :title="commentDialog.title" :visible.sync="commentDialog.visible">
      <!-- {{commentDialog.form}} -->
      <el-form ref="commentForm" :rules='rules' :model="commentDialog.form" label-position='left' size='small'>
        <!--        <el-form-item label="栏目名称" label-width="100px" prop="name">-->
        <!--          <el-input v-model="commentDialog.form.name" autocomplete="off"></el-input>-->
        <!--        </el-form-item>-->
        <el-form-item label="评论文章" label-width="100px">
          <el-select style='width:100%' v-model="commentDialog.form.articleid" placeholder="一级栏目">
            <el-option :key='index' v-for='(c,index) in articles' :label="c.title" :value="c.id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="描述信息" label-width="100px">
          <el-input
            type="textarea"
            :rows="2"
            placeholder="请输入内容"
            v-model="commentDialog.form.content">
          </el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <!--        <el-button size='small' @click='closeCommentDialog'>取 消</el-button>-->
        <el-button size='small' type="primary" @click='publishComment'>确 定</el-button>
      </div>
    </el-dialog>
    <!-- 模态框结束 -->
  </div>

</template>
<script>
  import axios from '@/http/axios'

  export default {
    data() {
      return {
        loading: false,
        multipleSelection: [],
        total: 0,
        comments: [],
        articles: [],
        params: {
          page: 1,
          pageSize: 6,
        },
        commentDialog: {
          title: '',
          visible: false,
          form: {
            liststyle: 'style-one',
            fileIds: []
          },
          fileList: []
        },
        rules: {
          title: [{
            required: true,
            message: '请输入标题',
            trigger: 'blur'
          }],
          categoryId: [{
            required: true,
            message: '请选择栏目',
            trigger: 'change'
          }]
        }
      }
    },
    watch: {
      // 只要params中的任意参数发生改变，就要刷新页面
      params: {
        handler: function () {
          this.findAllComments();
        },
        deep: true
      }
    },
    created() {
      // this.findAllCategories();
      this.findAllComments();
      this.findAllArticles()
    },
    methods: {
      resetForm() {
        this.$refs.commentForm.resetFields();
      },

      toAddComment() {
        this.commentDialog.title = '发布评论';
        this.commentDialog.visible = true;
      },
      toUpdateComment() {

      },
      publishComment() {
        axios.post('/manager/comment/publishComment', this.commentDialog.form).then(({data: result}) => {
          this.closeCommentDialog();
          this.$notify({
            title: '成功',
            message: result.message,
            type: 'success'
          });
          this.findAllComments();
        }).catch(() => {
          this.$notify.error({
            title: '错误',
            message: '添加异常'
          });
        });
      },
      findAllArticles() {
        let _this = this;
        axios.get('/manager/article/findArticle').then(function (response) {
          if (response.data.message == 'success') {
            _this.articles = response.data.data;

          }

        }).catch(function (error) {
          alert(error);
        })
      },
      batchDeleteComment() {
        let ids = this.multipleSelection.map((item) => {
          return item.id;
        })
        this.$confirm('此操作将永久删除该评论，是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
          .then(() => {
            let url = '/manager/comment/batchDeleteComment';
            axios.post(url, {ids: ids})
              .then(({data: result}) => {
                this.$notify({
                  title: '成功',
                  message: result.message,
                  type: 'success'
                });
                this.findAllComments();
              })
              .catch(() => {
                this.$notify.error({
                  title: '错误',
                  message: '删除异常'
                });
              });
          })
          .catch(() => {
          });
      },


      handleSelectionChange(val) {
        this.multipleSelection = val;
      },

      closeCommentDialog() {
        this.resetForm();
        this.commentDialog.visible = false;
      },


      // 查询所有栏目
      // findAllCategories(){
      // 	let url = '/manager/category/findAllCategory';
      // 	axios.get(url).then(({data:result})=>{
      // 		this.categories = result.data;
      // 	}).catch((error)=>{
      // 		this.$notify.error({
      // 			title:'错误',
      // 			message:'网络异常'
      // 		})
      // 	})
      // },
      handleCurrentChange(page) {
        this.params.page = page;
      },
      // 查询所有文章
      findAllComments() {
        this.loading = true;
        let url = '/manager/comment/findComment';
        axios.get(url, {
          params: this.params
        })
          .then(({data: result}) => {
            // 将查询到的数据绑定到模型中
            console.log(result);
            this.total = result.total;
            this.comments = result.list;
          }).catch((error) => {
          this.$notify.error({
            title: '错误',
            message: '网络异常'
          })
        }).finally(() => {
          this.loading = false;
        });
      },
      deleteComment(id) {
        this.$confirm('此操作将永久删除该数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
          .then(() => {
            let url = '/manager/comment/deleteCommentById';
            axios.get(url, {params: {id: id}})
              .then(({data: result}) => {
                this.$notify({
                  title: '成功',
                  message: result.message,
                  type: 'success'
                });
                this.findAllComments();
              })
              .catch(() => {
                this.$notify.error({
                  title: '错误',
                  message: '删除异常'
                });
              });
          })
          .catch(() => {
          });
      },

    }
  }
</script>

<style scoped>
  .list_style {

  }

  .list_style > div {
    width: 200px;
    height: 80px;
    overflow-y: hidden;
    border: 1px solid #ededed;
    padding: .5em;
    border-radius: 5px;
  }

  .list_style > div.current {
    border-color: #419fff;
  }

  .list_style img {
    width: 100%;
  }

  .list_style > div.list_one {
    float: left;
  }

  .list_style > div.list_two {
    margin-left: 220px;
  }
</style>


