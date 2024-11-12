<!--
Element-ui框架实现对数据库的增删改查
这里利用Vue进行简单的增删改查操作，可以与后台进行结合，进行增删改查的过程，只需要将接口改一下名字。
其中element-ui组件库经常与vue进行结合使用
vue被称为组件，那是因为一个vue文件由HTML、JS、CSS构成
 -->
 <!--一、 HTML部分，用的是element-ui 模板template -->
<template>
	<div class="back">
    <h1>{{ msg }}</h1>
		<el-button :span="8" type="primary" @click="addbook" class="add-btn" plain>
			添加图书
		</el-button>
		<el-input :span="8" placeholder="请输入编号搜索" v-model="id" class="add-btn"
		clearable>
		</el-input>
		<el-button :span="8" slot="append" icon="el-icon-search" @click="check"
		class="button">
		</el-button>
		<el-table :data="bookInfo" style="width: 100%">
			<el-table-column prop="id" label="编号" width="180">
			</el-table-column>
			<el-table-column prop="title" label="书名" width="180">
						<template slot='header'>
							<span class ="star">*</span>
							<span >书名</span>
						</template>
			</el-table-column>
			<el-table-column prop="author" label="作者" width="180">
						<template slot='header'>
							<span class ="star">*</span>
							<span >作者</span>
						</template>
			</el-table-column>
			<!-- 表头提示在这里用 -->
			<el-table-column prop="read_status" label="阅读状态" width="180" :render-header="renderHeader">
				<!-- 这里红星星没生效还需要找原因 -->
						<template slot='header'>
							<span class ="star">*</span>
							<span >阅读状态</span>
						</template>
			</el-table-column>
			<el-table-column align="right">
				<template slot-scope="scope">
					<!-- plain改变按钮显示方式,element 称为朴素按钮-->
					<el-button size="mini" type="info" @click="detail(scope.row,scope.row.id)" >
            详情
          </el-button>
					<el-button size="mini" type="success" @click="edit(scope.row,scope.row.id)">
						编辑
					</el-button>
					<el-button size="mini" type="danger" @click.native.prevent="deleteRow(scope.row.id)">
						删除
					</el-button>
				</template>
			</el-table-column>
		</el-table>
		<!-- 数据分页页面，还没做好-->
		<div class="block" >
			<!--换行用br标签-->
			<br><br/>
			<el-pagination
			@size-change="handleSizeChange"
			@current-change="handleCurrentChange"
			:current-page="currentPage4"
			:page-sizes="[20, 30, 50, 100]"
			:page-size="100"
			layout="total, sizes, prev, pager, next, jumper"
			:total="400">
			</el-pagination>
		</div>
		<!--增加对话框-->
		<el-dialog title="增加图书信息" :visible.sync="addbook_dialogVisible" width="30%" :before-close="handleClose1">
			<div>
				<el-form ref="form" :model="editObj1" label-width="80px">
					<el-form-item label="书名" required="true">
						<el-input v-model="editObj1.title" placeholder="请输入书名">
						</el-input>
					</el-form-item>
					<el-form-item label="作者" required="true">
						<el-input v-model="editObj1.author" placeholder="请输入作者">
						</el-input>
					</el-form-item>
					<!--鼠标在此标签区域显示提示-->
					<el-form-item label="阅读状态" required="true" title="阅读状态只能输入0或者1;0代表未读,1代表已读">
						<el-input v-model="editObj1.read_status" placeholder="请输入阅读状态">
						</el-input>
					</el-form-item>
				</el-form>
			</div>
			<span slot="footer" class="dialog-footer">
				<el-button @click="addbook_dialogVisible = false">取消</el-button>
				<el-button type="primary" @click="confirm_add">确定</el-button>
			</span>
		</el-dialog>
		<!--编辑对话框 -->
		<el-dialog title="编辑图书信息" :visible.sync="edit_dialogVisible" width="30%" :before-close="handleClose">
			<div>
				<el-form ref="form" :model="editObj" label-width="80px">
					<el-input type="hidden" v-model="editObj.id"></el-input>
					<!--required显示红星表示必填项，placeholder为输入框注释 -->
					<el-form-item label="书名" required="true" >
						<el-input v-model="editObj.title" placeholder="请输入书名"></el-input>
					</el-form-item>
					<el-form-item label="作者" required="true" >
						<el-input v-model="editObj.author" placeholder="请输入作者"></el-input>
					</el-form-item>
					<el-form-item label="阅读状态" required="true" title="阅读状态只能输入0或者1;0代表未读,1代表已读">
						<el-input v-model="editObj.read_status" placeholder="请输入阅读状态"></el-input>
					</el-form-item>
				</el-form>
			</div>
			<span slot="footer" class="dialog-footer">
				<el-button @click="edit_dialogVisible = false">取消</el-button>
				<el-button type="primary" @click="confirm">确定</el-button>
			</span>
		</el-dialog>
		<!--详情对话框，新建一个对话框把编辑的代码复制过来，然后逻辑复制编辑额逻辑，再把确定和取消按钮去掉即可 -->
				<el-dialog title="图书详情信息" :visible.sync="detail_dialogVisible" width="30%" :before-close="detail_handleClose">
			<div>
				<el-form ref="form" :model="editObj" label-width="80px">
					<el-input type="hidden" v-model="editObj.id"></el-input>
					<el-form-item label="书名" required="true">
						<el-input v-model="editObj.title" :disabled="true"></el-input>
					</el-form-item>
					<el-form-item label="作者" required="true">
						<el-input v-model="editObj.author" :disabled="true"></el-input>
					</el-form-item>
					<el-form-item label="阅读状态" required="true" title="阅读状态只能输入0或者1;0代表未读,1代表已读">
						<el-input v-model="editObj.read_status" :disabled="true"></el-input>
					</el-form-item>
				</el-form>
			</div>
			<!--详情对话框的确定和取消按钮去掉
			<span slot="footer" class="dialog-footer">
				<el-button @click="edit_dialogVisible = false">取消</el-button>
				<el-button type="primary" @click="confirm">确定</el-button>
			</span>
			-->
		</el-dialog>
	</div>
</template>

<!--二、下面是JS部分-->
<script>
export default {
  data () {
    return {
      msg: '欢迎访问图书管理系统',
      edit_dialogVisible: false,
      addbook_dialogVisible: false,
      detail_dialogVisible: false,
      id: '',
      title: '',
      author: '',
      read_status: '',
      bookInfo: [{
        id: '',
        title: '',
        author: '',
        read_status: ''
      }],
      editObj: {
        id: '',
        title: '',
        author: '',
        read_status: ''
      },
      editObj1: {
        title: '',
        author: '',
        read_status: ''
      },
      userIndex: 0
    }
  },
  mounted () {
    this.check()
  },
  methods: {
    // 1、显示方法
    getnetwork () {
      // console.log(this.bookinfo.id);
      var that = this
      this.$axios.post('http://127.0.0.1:5001/query', {
        id: '' // 这里不传id查所有
      })
        .then(function (res) {
          console.log(res.data)
          that.bookInfo = res.data.data
          that.bookInfo = that.bookInfo.map(item => {
            item.read_status = String(item.read_status)
            return item
          })
        // console.log(this.bookInfo)
        }).catch(function (err) {
          console.log(err)
        })
    },

    // 2、查询方法
    check () { // 确定查询按钮
      // console.log(this.bookname)
      var that = this
      // 网络请求获取数据axios
      this.$axios.post('http://127.0.0.1:5001/query', {
        id: this.id
      }).then(function (res) { // 请求成功，方法回调
        // 回调方法不能用this
        console.log(res.data)
        console.log(res.data.data)
        that.bookInfo = res.data.data
        that.bookInfo = that.bookInfo.map(item => { // 布尔类型转字符串
          item.read_status = String(item.read_status)
          return item
        })
        // console.log(this.data.id)
      }).catch(function (err) { // 请求失败
        console.log(err)
      })
    },
    addbook () {
      this.addbook_dialogVisible = true
    },
    edit (item, id) {
      this.id = id
      console.log(id)
      this.editObj = {
        id: item.id,
        title: item.title,
        author: item.author,
        read_status: item.read_status
      }
      this.edit_dialogVisible = true
    },
    detail (item, id) {
      this.id = id
      console.log(id)
      this.editObj = {
        id: item.id,
        title: item.title,
        author: item.author,
        read_status: item.read_status
      }
      this.detail_dialogVisible = true
    },
    // 3、删除方法
    deleteRow (id) {
      console.log(id)
      if (confirm('确定要删除吗') === true) {
        // var that = this
        // 网络请求获取数据axios
        this.$axios.post('http://127.0.0.1:5001/delete', {
          id: id
        })
          .then(res => { // 请求成功，方法回调
            // 回调方法不能this
            this.getnetwork()
            // getnetwork();
            console.log(res.data)
          }).catch(function (err) { // 请求失败
            console.log('失败了' + err)
          })
      }
    },
    // 对话框右上角的×按钮
    handleClose () { // 编辑
      this.edit_dialogVisible = false
    },
    handleClose1 () { // 增加
      this.addbook_dialogVisible = false
    },
    detail_handleClose () { // 详情
      this.detail_dialogVisible = false
    },
    // 4、修改方法
    confirm () { // 确认修改
      if (this.editObj.title === '') {
        this.$message({
          message: '书名不能为空！',
          type: 'warning'
        })
        return
      }
      if (this.editObj.author === '') {
        this.$message({
          message: '作者不能为空！',
          type: 'warning'
        })
        return
      }
      if (this.editObj.read_status === '') {
        this.$message({
          message: '阅读状态不能为空！',
          type: 'warning'
        })
        return
      }
      var that = this
      // 网络请求获取数据axios
      this.$axios.post('http://127.0.0.1:5001/update', {
        id: this.editObj.id,
        title: this.editObj.title,
        author: this.editObj.author,
        read_status: this.editObj.read_status
      }).then(function (res) { // 请求成功，方法回调
      // 回调方法不能this
        that.getnetwork()
        console.log(res.data)
      }).catch(function (err) { // 请求失败
        console.log(err)
      })
      this.edit_dialogVisible = false
      // Vue.set(this.tableData, this.userIndex, this.editObj);
    },
    // 5、 增加方法
    confirm_add () { // 确定增加按钮
      if (this.editObj1.title === '') {
        this.$message({
          message: '书名不能为空！',
          type: 'warning'
        })
        return
      }
      if (this.editObj1.author === '') {
        this.$message({
          message: '作者不能为空！',
          type: 'warning'
        })
        return
      }
      if (this.editObj1.read_status === '') {
        this.$message({
          message: '阅读状态不能为空！',
          type: 'warning'
        })
        return
      }
      var that = this
      // 网络请求获取数据axios
      this.$axios.post('http://127.0.0.1:5001/add', {
        title: this.editObj1.title,
        author: this.editObj1.author,
        read_status: this.editObj1.read_status
      }).then(function (res) { // 请求成功，方法回调
        //	this.$message.success('图书添加成功!');

        // 回调方法不能用this
        that.getnetwork()
        // if (this.editObj1.read_status == success)
        console.log(res.data)
        // that.newsList = res.data
      }).catch(function (err) { // 请求失败
        console.log(err)
      })
      this.addbook_dialogVisible = false
      // Vue.set(this.tableData, this.userIndex, this.editObj);
    },
    // 给表头增加提示
    renderHeader (h, {column}) {
      const serviceContent = [
        h('div', {
          slot: 'content'
        },
        '提示:true代表已读,false代表未读'
        )
      ]
      return h('div', [
        h('span', column.label),
        h('el-tooltip', {
          props: {
            placement: 'top'
          }
        },
        [
          serviceContent,
          h('i', {
            class: 'el-icon-warning-outline',
            style: 'color:blue;margin-left:5px;'
          })
        ])
      ])
    }
  }
}
</script>
<!-- 三、CSS部分 颜色、样式自己调试，这里就能体会有UI工程师的好处-->
<style>
/**标签为class属性就用.修饰，标签属性为id的话用#修饰。**/
	.add-btn{ width:450px }
    .input{ width: 220px; height: 70px; }
    .button{
		color: rgb(246, 241, 248);
    background-color: rgb(187, 187, 221);
	}
	#app{ width: 1024px; margin: 0 auto; }
    #back{ height: 200px; }
	.block{text-align:left}
	.star{
		color: #F56C6C;
		font-size: 14px;
		margin-right: 4px;
	}
</style>
