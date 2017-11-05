<template>
	<div class="table">
		<div class="crumbs">
			<el-breadcrumb separator="/">
				<el-breadcrumb-item :to="{ path: '/main' }"><i class="el-icon-menu"></i>首页</el-breadcrumb-item>
				<el-breadcrumb-item>餐厅列表</el-breadcrumb-item>
			</el-breadcrumb>
		</div>
		<div class="handle-box">

			<el-input v-model="select_word" placeholder="筛选关键词" class="handle-input mr10"></el-input>
			<el-button type="primary" icon="search" @click="search">搜索</el-button>
			<el-button type="primary" icon="plus" @click="Register">注册账号</el-button>
		</div>
		<el-table :data="listData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">

			<el-table-column prop="auth_info" label="设备ID" sortable width="150">
			</el-table-column>
			<el-table-column prop="name" label="餐厅名" width="120">
			</el-table-column>
			<el-table-column prop="state" label="状态" width="120">
			</el-table-column>

			<el-table-column prop="location" label="地址">
			</el-table-column>
			<el-table-column label="操作" width="180">
				<template scope="scope">
					<el-button size="small" @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
					<el-button size="small" type="danger" @click="handleDelete(scope.$index, scope.row)">删除</el-button>
				</template>
			</el-table-column>
		</el-table>
		<div class="pagination">
			<el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="count">
			</el-pagination>
		</div>
	</div>
</template>

<script>
	export default {
		data() {
			return {
				listData: [],
				page: 1,
				count: 100,

				url: './static/vuetable.json',
				tableData: [],
				multipleSelection: [],
				select_cate: '',
				select_word: '',
				del_list: [],
				is_search: false
			}
		},
		created() {
			this.getData();
			
			const num = 5;
			console.log('整数'+Number.isInteger(num))
		},
		computed: {},
		methods: {
			//注册账号
			Register() {

				this.$prompt('请输入鉴权信息', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					inputPattern: /\S/,
					inputErrorMessage: '必须填写鉴权信息'
				}).then(({
					value
				}) => {

					this.$http.post('/admin/signup', {
						auth_info: value
					}).then(response => {
						if(response.data.result == '注册成功') {
							this.$message({
								type: 'success',
								message: '注册成功: ' + value
							});
							this.getData();
						} else {
							this.$message({
								type: 'info',
								message: '注册失败' + JSON.stringify(response.data)
							});
						}
					})

				}).catch(() => {
					this.$message({
						type: 'info',
						message: '取消注册'
					});
				});
			},

			toRegister() {
				this.$router.push('/register');
			},
			//换页，改变当前页数并且获取数据
			handleCurrentChange(val) {
				console.log(val)
				this.page = val;
				this.getData();
			},
			//根据页数获取数据
			getData() {
				console.log('发送页数'+ this.page)
				this.$http.post('/admin/searchUser', {
					page: this.page,
					fuck:'2'
				}).then(response => {
					this.listData = response.data.user
					this.count = response.data.count
					console.log(JSON.stringify(response.data))
				})
			},
			search() {
				this.is_search = true;
			},
			formatter(row, column) {
				return row.address;
			},
			filterTag(value, row) {
				return row.tag === value;
			},
			handleEdit(index, row) {
				this.$message('编辑第' + (index + 1) + '行');
			},
			handleDelete(index, row) {
				this.$message.error('删除第' + (index + 1) + '行');
			},
			delAll() {
				const self = this,
					length = self.multipleSelection.length;
				let str = '';
				self.del_list = self.del_list.concat(self.multipleSelection);
				for(let i = 0; i < length; i++) {
					str += self.multipleSelection[i].name + ' ';
				}
				self.$message.error('删除了' + str);
				self.multipleSelection = [];
			},
			handleSelectionChange(val) {
				this.multipleSelection = val;
			}
		}
	}
</script>

<style scoped>
	.handle-box {
		margin-bottom: 20px;
	}
	
	.handle-select {
		width: 120px;
	}
	
	.handle-input {
		width: 300px;
		display: inline-block;
	}
</style>