<template>
  <div class="cateManage">
    <div class="rightbook">
      <div class="herderOption">

        <!-- <el-select v-model="searchAllCategory" placeholder="分类筛选" filterable @change='searchBookCate' class="selectCate">
					<el-option  v-for="item in bookTableData" :key="item.id" :label="item.type" :value="item.id"></el-option>
				</el-select> -->

        <el-button type="primary" round class="addBtn" @click="addBookCategory = true">添加</el-button>
        <!-- <el-button lass="addBtn" type="success" @click="resetAll" round>重置</el-button> -->
      </div>
      <!-- 添加分类 dialog -->
      <el-dialog title="添加分类" :visible.sync="addBookCategory">
        <el-form :model="form">
          <el-form-item label="方向" :label-width="formLabelWidth">
            <el-select v-model="form.nameDirection" filterable allow-create default-first-option placeholder="请选择或输入方向">
              <el-option v-for="item in this.bookTableData" :key="item.category" :label="item.category" :value="item.category">
              </el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="分类" :label-width="formLabelWidth">
            <el-input v-model="form.nameCategory" auto-complete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="addBookCategory = false">取 消</el-button>
          <el-button type="primary" @click="addBookCate()">确 定</el-button>
        </div>
      </el-dialog>
      <!-- 分类渲染 -->
      <el-table :data="bookTableData" v-loading="loading" :default-sort="{prop: 'category', order: 'descending'}">
        <el-table-column prop="category" label="方向" align="center" sortable>
        </el-table-column>
        <el-table-column prop="" label="分类" align="center">
          <template slot-scope="scope">
            <el-select v-model="selectTypeModel[scope.$index]" @visible-change="getType(scope.$index, scope.row)"
              placeholder="请选择" size="small">
              <el-option v-for="(item,index) in selectType" :key="item.id" :label="item.type" :value="item.id">
              </el-option>
            </el-select>
          </template>
        </el-table-column>
        <el-table-column label="操作" prop="" class="options" align="center">
          <template slot-scope="scope">
            <el-button type="text" @click="getBookDelRow(scope.$index, scope.row)">删除</el-button>
            <el-dialog title="提示" :visible.sync="dialogVisible" width="30%">
              <span>确认删除?</span>
              <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="danger" @click.native.prevent="deleteBookRow()">确 定</el-button>
              </span>
            </el-dialog>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>

</template>

<script>
  export default {
    data() {
      return {
        loading: true,
        allCategory: [],
        searchAllCategory: '',
        dialogVisible: false,
        delIndex: 0,
        delRows: 0,
        bookTableData: [],
        selectType: [],
        selectTypeModel: [],
        addBookCategory: false,
        options: [],
        form: {
          nameDirection: '',
          nameCategory: '',
        },
        formLabelWidth: '120px',
      };
    },
    created() {
      this.getBookCategory()
    },
    methods: {
      // 图书大分类
      getBookCategory() {
        this.$axios.get('/category')
          .then(results => {
            this.bookTableData = results.data
            this.loading = false
          })
          .catch(() => {
            this.$message.error('出现错误,请刷新再试或联系管理员')
          })
      },
      // 请求子分类
      getType(index, rows) {
        this.$axios
          .get('/category/type?category=' + rows.category)
          .then((results) => {
            this.selectType = results.data
          })

      },
      // 获取当前删除行
      getBookDelRow(index, rows) {
        this.dialogVisible = true
        this.delIndex = index
        this.delRows = rows
      },
      // 删除子分类
      deleteBookRow() {
        this.dialogVisible = false
        this.$axios.delete('/category/' + this.selectTypeModel)
          .then(() => {
            this.selectTypeModel = ''
            this.$message('删除成功')
          })
      },
      // 添加分类
      addBookCate() {

        if (this.form.nameDirection == '' || this.form.nameCategory == '') {
          this.$message.error('输入不能为空')
        } else {
          this.$axios.post('/category', {
              category: this.form.nameDirection,
              type: this.form.nameCategory
            })
            .then(() => {
              this.$message('添加成功')
              this.getBookCategory()
              this.addBookCategory = false
            })
            .catch((err) => {
              this.$message.error('出现错误,请刷新再试或联系管理员')
            })
        }
      },
      // searchBookCate(value) {
      // 	 axios.get('/category')
      // 		.then(results => {
      // 			console.log(results.data)
      // 			this.allCategory = results.data
      // 		})
      // },
      // ************************
      resetAll() {
        this.addBook = ''
        this.getBookCategory()
      },
    }
  }

</script>

<style lang="css" scoped>
  .cateManage:after {
    content: ".";
    clear: both;
    display: block;
    height: 0;
    overflow: hidden;
    visibility: hidden;
  }

  .rightbook {
    /* width: 70%;	 */
    /* margin-top: 15px; */
  }

  .herderOption {
    margin-bottom: 15px;
  }

  .el-input {
    width: 50%;
    margin-bottom: 10px;
  }

  .addBtn {
    /* margin-left: 30px; */
  }

  .resetBtn {
    top: 50px;
    position: relative;
    text-align: center;
  }

</style>
