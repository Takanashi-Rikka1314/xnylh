<template>
	<div class="home">
		<p>
			<button @click="suo">{{ s }}</button>
		</p>
		<p>所在城市：
			<select v-model="city">
				<option value="">请选择城市</option>
				<option v-for="item in list" :value="item.name" :key="item.id">{{ item.name }}</option>
			</select>
		</p>
		<p>用户名：<input type="text" :disabled="flag1" v-model="username"></p>
		<p>年龄：<input type="number" :disabled="flag1" v-model="age"></p>
		<p><input type="button" :disabled="flag1" @click="add" value="提交"></p>
		<p>
			<span class="s">年龄区间：
				<input type="number" v-model="age1" @keydown.enter="search('age')" placeholder="最小年龄">-
				<input type="number" v-model="age2" @keydown.enter="search('age')" placeholder="最大年龄">
				<button @click="search('age')">🔍 搜索</button>
			</span>
			<span class="s">
				<input type="text" placeholder="搜索城市" v-model="city1" @keydown.enter="search('city')">
				<button @click="search('city')">🔍 搜索</button>
			</span>
			<span class="s">
				<input type="text" placeholder="搜索用户名" v-model="name" @keydown.enter="search('name')">
				<button @click="search('name')">🔍 搜索</button>
			</span>
			<button class="s an" @click="sort('age',0)">年龄升序</button>
			<button class="s an" @click="sort('age',1)">年龄降序</button>
			<button class="s an" @click="sort('city',0)">城市首字母升序</button>
			<button class="an" @click="sort('city',1)">城市首字母降序</button>
		</p>
		<table>
			<tr>
				<th></th>
				<th class="i">id</th>
				<th>姓名</th>
				<th>年龄</th>
				<th class="gu">城市</th>
				<th class="gu">操作</th>
			</tr>
			<tbody v-if="userList.length">
			<tr v-for="item in userList" :key="item.id" class="active">
				<td><input type="checkbox" :disabled="flag1" :value="item.id" v-model="checkedList"></td>
				<td class="i">{{ item.id }}</td>
				<td>{{ item.name }}</td>
				<td class="gu">{{ item.age }}</td>
				<td class="gu">{{ item.city }}</td>
				<td>
					<input type="button" :disabled="flag1" @click="set(item.id)" value="修改">
					<input type="button" :disabled="flag1" @click="del(item.id)" value="删除">
				</td>
			</tr>
			</tbody>
		</table>
		<p><input type="button" :disabled="flag1" @click="checkedDel" value="删除所选"></p>
	</div>
</template>

<script>
import data from '@/assets/data.json';

export default {
	name: 'Home',
	data() {
		return {
			list: data.data,
			username: '',
			name: '',
			age: '',
			age1: '',
			age2: '',
			city: '',
			city1: '',
			userList: [],
			checkedList: [],
			flag: false,
			id: 0,
			flag1: false,
			s: '锁定',
			list1: []
		}
	},
	methods: {
		sort(val, b) {
			if (b) {
				this.userList.sort((a, b) => {
					return b.val - a.val;
				});
			} else {
				this.userList.sort((a, b) => {
					return a.val - b.val;
				});
			}
		},
		search(val) {
			this.userList = this.list1;
			let arr = [];
			if (val === 'age') {
				if (this.age1 && this.age2) {
					arr = this.userList.filter(item => {
						return item.age >= this.age1 && item.age <= this.age2;
					});
				}
				if (this.age1 && !this.age2) {
					arr = this.userList.filter(item => {
						return item.age >= this.age1;
					});
				}
				if (!this.age1 && this.age2) {
					arr = this.userList.filter(item => {
						return item.age <= this.age2;
					});
				}
				this.userList = arr;
			}
			if (val === 'city') {
				this.userList = this.list1;
				let arr = [];
				arr = this.userList.filter(item => {
					return item.city === this.city1;
				});
				this.userList = arr;
			}
			if (val === 'name') {
				this.userList = this.list1;
				let arr = [];
				arr = this.userList.filter(item => {
					return item.val.includes(this.val);
				});
				this.userList = arr;
			}
		},
		add() {
			// if (!this.city || !this.username || !this.age) {
			// 	alert('请将用户信息补充完成!');
			// 	return;
			// }
			if (this.flag) {
				this.userList.forEach(item => {
					if (item.id === this.id) {
						item.city = this.city;
						item.name = this.username;
						item.age = this.age;
					}
				});
				this.flag = false;
				this.id = 0;
			} else {
				let obj = {
					id: new Date().getTime(),
					name: this.username,
					city: this.city,
					age: this.age,
				};
				this.userList.push(obj);
			}
			this.list1 = this.userList;
			this.city = '';
			this.username = '';
			this.age = '';
		},
		del(id) {
			this.userList.forEach((item, index, arr) => {
				if (item.id === id) {
					arr.splice(index, 1);
				}
			});
		},
		checkedDel() {
			this.checkedList.forEach(item => {
				this.userList.forEach((e, i, arr) => {
					if (item === e.id) {
						arr.splice(i, 1);
					}
				});
			});
		},
		set(id) {
			this.flag = true;
			this.userList.forEach(item => {
				if (item.id === id) {
					this.city = item.city;
					this.username = item.name;
					this.age = item.age;
					this.id = id;
				}
			});
		},
		suo() {
			if (!this.flag1) {
				this.s = '解锁';
				this.flag1 = true
			} else {
				let n = 3;
				this.s = n + '秒后解锁';
				let timeId = setInterval(() => {
					n--;
					this.s = n + '秒后解锁';
					if (n < 1) {
						clearInterval(timeId);
						this.s = '锁定';
						this.flag1 = false;
					}
				}, 1000);
			}
		}
	}
}
</script>

<style lang="scss" scoped>
.home {
  table {
    width: 100%;
    text-align: center;
    border-collapse: collapse;
  }

  .active:nth-child(2n) {
    background-color: #91BEF0;
  }

  .active:nth-child(2n-1) {
    background-color: pink;
  }

  .an {
    cursor: pointer;
  }

  .s {
    float: left;
    margin: 30px 36px;

    button {
      margin-left: 12px;
    }
  }

  .i, .gu {
    width: 180px;
  }

  th {
    background-color: #EBEBEA;
  }

  th, td, tr {
    height: 36px;
    line-height: 36px;
    border: 1px solid #ccc;
  }

  td:first-child, th:first-child {
    width: 139px;
  }

  td:last-child, th:last-child {
    width: 395px;
  }
}
</style>