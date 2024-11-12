<!--
Element-ui框架实现对数据库的增删改查
这里利用Vue进行简单的增删改查操作，可以与后台进行结合，进行增删改查的过程，只需要将接口改一下名字。
其中element-ui组件库经常与vue进行结合使用
 -->
<template>
  <div class="back">
    <h1>{{ msg }}</h1>
    <el-button :span="8" type="primary" @click="adduser" class="add-btn" plain>
      添加信息
    </el-button>
    <el-input :span="8" placeholder="请输入内容" v-model="bookname" class="add-btn" clearable></el-input>
		<el-button :span="8" slot="append" icon="el-icon-search" @click="check" class="button"></el-button>
    <el-table :data="bookInfo" style="width: 100%">
			<el-table-column prop="id" label="编号" width="180">
			</el-table-column>
			<el-table-column prop="name" label="书名" width="180">
			</el-table-column>
			<el-table-column prop="writer" label="作者" width="180">
			</el-table-column>
			<el-table-column prop="publish" label="出版社" width="180">
			</el-table-column>
			<el-table-column align="right">
				<!-- <template> -->
				<el-input placeholder="请输⼊书名搜索" v-model="bookname" clearable>
				</el-input>
				<el-button slot="append" icon="el-icon-search" @click="check" class="button">
				</el-button>
				<!-- </template> -->
				<template slot-scope="scope">
					<el-button size="mini" @click="edit(scope.row,scope.row.id)">
						编辑
					</el-button>
					<el-button size="mini" type="danger" @click.native.prevent="deleteRow(scope.row.id)">
						删除
					</el-button>
				</template>
			</el-table-column>
		</el-table>
		<!--增加框-->
		<el-dialog title="增加书籍信息" :visible.sync="dialogVisible1" width="30%" :before-close="handleClose1">
			<div>
				<el-form ref="form" :model="editObj1" label-width="80px">
					<el-form-item label="书名">
						<el-input v-model="editObj1.name">
						</el-input>
					</el-form-item>
					<el-form-item label="作者">
						<el-input v-model="editObj1.writer">
						</el-input>
					</el-form-item>
					<el-form-item label="出版社">
						<el-input v-model="editObj1.publish">
						</el-input>
					</el-form-item>
				</el-form>
			</div>
			<span slot="footer" class="dialog-footer">
				<el-button @click="dialogVisible1 = false">
					取消
				</el-button>
				<el-button type="primary" @click="confirm1">
					确定
				</el-button>
			</span>
		</el-dialog>
		<!--编辑框 -->
		<el-dialog title="编辑书籍信息" :visible.sync="dialogVisible" width="30%" :before-close="handleClose">
			<div>
				<el-form ref="form" :model="editObj" label-width="80px">
					<el-input type="hidden" v-model="editObj.id">
					</el-input>
					<el-form-item label="书名">
						<el-input v-model="editObj.name">
						</el-input>
					</el-form-item>
					<el-form-item label="作者">
						<el-input v-model="editObj.writer">
						</el-input>
					</el-form-item>
					<el-form-item label="出版社">
						<el-input v-model="editObj.publish">
						</el-input>
					</el-form-item>
				</el-form>
			</div>
			<span slot="footer" class="dialog-footer">
				<el-button @click="dialogVisible = false">
					取消
				</el-button>
				<el-button type="primary" @click="confirm">
					确定
				</el-button>
			</span>
		</el-dialog>
	</div>
</template>
<script>
export default {
  data () {
    return {
      msg: '欢迎访问图书管理系统',
      dialogVisible: false,
      dialogVisible1: false,
      bookname: '',
      bookInfo: [{
        id: '',
        name: '',
        writer: '',
        publish: ''
      }],
      editObj: {
        id: '',
        name: '',
        writer: '',
        publish: ''
      },
      editObj1: {
        name: '',
        writer: '',
        publish: ''
      },
      userIndex: 0
    }
  },
  mounted () {
    this.getnetwork()
  },
  methods: {
    getnetwork () {
      // console.log(this.bookinfo.id);
      var that = this
      this.$axios.post('http://localhost:8081/springboot/getuser')
        .then(function (res) {
          console.log(res.data)
          that.bookInfo = res.data
          // console.log(this.bookInfo)
        }).catch(function (err) {
          console.log(err)
        })
    },

    // 确定查询按钮
    check () {
      // console.log(this.bookname)
      var that = this
      // 网络请求获取数据axios
      this.$axios.post('http://localhost:8081/springboot/checkbyname', {
        name: this.bookname
      }).then(function (res) { // 请求成功，方法回调
        // 回调方法不能用this
        console.log(res.data)
        console.log(res.data.data)
        that.bookInfo = res.data.data
        // console.log(this.data.id)
      }).catch(function (err) { // 请求失败
        console.log(err)
      })
    },
    adduser () {
      this.dialogVisible1 = true
    },
    edit (item, id) {
      this.userIndex = id
      console.log(id)
      // console.log(item.id)
      // console.log(item.id)
      this.editObj = {
        id: item.id,
        name: item.name,
        writer: item.writer,
        publish: item.publish
      }
      this.dialogVisible = true
    },
    deleteRow (bookid) {
      console.log(bookid)
      if (confirm('确定要删除吗') === true) {
        var that = this
        // 网络请求获取数据axios
        this.$axios.post('http://localhost:8081/springboot/deletebyid', {
          id: bookid
        })
          .then(res => { // 请求成功，方法回调
            // 回调方法不能this
            this.getnetwork()
            // getnetwork();
            console.log(res.data)
            that.newsList = res.data
          // console.log(this.newsList);
          }).catch(function (err) { // 请求失败
            console.log('失败了' + err)
          })
      }
      // window.location.href = 'http://127.0.0.1:8848/HelloVue/book.html'
    },
    // ×按钮
    handleClose () {
      this.dialogVisible = false
    },
    handleClose1 () {
      this.dialogVisible1 = false
    },
    confirm () { // 确认修改
      console.log(this.editObj.id)
      console.log(this.editObj.name)
      console.log(this.editObj.publish)
      console.log(this.editObj.writer)
      var that = this
      // 网络请求获取数据axios
      this.$axios.post('http://localhost:8081/springboot/updateuser', {
        id: this.editObj.id,
        name: this.editObj.name,
        publish: this.editObj.publish,
        writer: this.editObj.writer
      }).then(function (res) { // 请求成功，方法回调
        // 回调⽅方不能this
        that.getnetwork()
        console.log(res.data)
        that.newsList = res.data
      }).catch(function (err) { // 请求失败
        console.log(err)
      })
      this.dialogVisible = false
      // Vue.set(this.tableData, this.userIndex, this.editObj);
    },
    // 确定增加按钮
    confirm1 () {
      var that = this
      // 网络请求获取数据axios
      this.$axios.post('http://localhost:8081/springboot/adduser', {
        name: this.editObj1.name,
        publish: this.editObj1.publish,
        writer: this.editObj1.writer
      })
        .then(function (res) { // 请求成功，方法回调
          // 回调方法不能用this
          that.getnetwork()
          console.log(res.data)
          that.newsList = res.data
        }).catch(function (err) { // 请求失败
          console.log(err)
        })
      this.dialogVisible1 = false
      //  Vue.set(this.tableData, this.userIndex, this.editObj);
    }
  }
}
</script>
<style>
	.add-btn{ width:450px }
  .input{ width: 220px; height: 70px; }

  .button{}

	#app{ width: 1024px; margin: 0 auto; }
  #back{ height: 200px; }
</style>
