<template>
	<div class="home">
		<div class="login">
			<p>用户名：
				<span v-if="!flag">{{ username }}</span>
				<input v-if="flag" type="text" :class="flag1 ? 'active' : ''" v-model="username">
				<span v-if="flag1" :class="flag1 ? 'active' : ''">用户名必须是6~18位数字、字母、下划线。</span>
			</p>
			<button v-if="flag" @click="login">登录</button>
			<button v-if="!flag" @click="out">退出</button>
		</div>
		<div class="wt" v-if="e >= 0">
			<h1>{{ list[i].title }}</h1>
			<p v-for="item in list[i].options">
				<input type="radio" :value="item" v-model="option">
				<span class="d">{{ item }}</span>
			</p>
			<button @click="submit" v-if="!msg">提交答案</button>
			<button disabled v-if="msg">{{ msg }}</button>
			<p v-if="msg">{{ second }}s后进入下一题</p>
			<p class="foot">共有{{ i + 1 }}/{{ list.length }}道题，答对：{{ this.obj.correct.length }}
				答错：{{ this.obj.error.length }}</p>
		</div>
		<div v-if="e < 0" class="wt">
			<p>没有了。
				<button @click="regression">重做一遍</button>
			</p>
			<p class="foot">共有{{ i + 1 }}/{{ list.length }}道题，答对：{{ this.obj.correct.length }}
				答错：{{ this.obj.error.length }}</p>
		</div>
		<div class="zz" v-if="flag"></div>
	</div>
</template>

<script>
import data from '@/assets/data.json';

export default {
	name: 'Home',
	components: {},
	data() {
		return {
			username: '',
			res: /^\w{6,18}$/,
			flag: true,
			list: data.data,
			i: 0,
			option: '',
			obj: {
				correct: [],
				error: []
			},
			flag1: false,
			e: 3,
			msg: '',
			second: 3
		}
	},
	methods: {
		login() {
			if (!this.username) {
				alert('请输入用户名!');
				return;
			}
			if (this.res.test(this.username)) {
				this.flag = false;
				this.flag1 = false;
			} else {
				this.flag1 = true;
			}
		},
		out() {
			this.i = 0;
			this.e = 3;
			this.obj.error = [];
			this.obj.correct = [];
			this.username = '';
			this.flag = true;
		},
		submit() {
			if (!this.option) {
				alert('未作答!');
			} else {
				if (this.option === this.list[this.i].options[this.e]) {
					this.obj.correct.push(true);
					this.msg = '回答正确';
				} else {
					this.obj.error.push(false);
					this.msg = '回答错误';
				}
				let timeId = setInterval(() => {
					this.second--;
					if (this.second < 0) {
						clearInterval(timeId);
						this.e--;
						this.i++;
						this.msg = '';
						this.option = '';
						this.second = 3;
						if (this.i > 3) {
							this.i = 3;
						}
					}
				}, 1000);
			}
		},
		regression() {
			this.i = 0;
			this.e = 3;
			this.obj.error = [];
			this.obj.correct = [];
		}
	}
}
</script>

<style lang="scss" scoped>
.home {
  width: 98%;
  height: 600px;
  margin: 60px auto;
  display: flex;
  justify-content: space-between;
  position: relative;

  button {
    cursor: pointer;
    padding: 6px 12px;
    font-size: 18px;
    border-radius: 3px;
    margin-top: 36px;
    border: 1px solid;
    outline: none;
  }

  h1 {
    margin-bottom: 30px;
  }

  .d {
    margin-left: 12px;
    font-size: 18px;
  }

  div {
    width: 50%;
    height: 600px;
    border: 1px solid #999;
  }

  .wt {
    height: 600px;
    box-sizing: border-box;
    padding: 12px 0 0 12px;
    position: relative;

    .foot {
      position: absolute;
      left: 12px;
      bottom: 12px;
    }

    p {
      font-size: 18px;
    }
  }

  .zz {
    position: absolute;
    top: 0;
    right: 0;
    background-color: rgba(0, 0, 0, 0.6);
  }

  .login {
    font-size: 22px;
    box-sizing: border-box;
    padding: 12px 0 0 12px;

    input {
      width: 240px;
      height: 36px;
      text-indent: 12px;
      border-radius: 5px;
      border: 1px solid #ccc;
      outline: none;
    }

    .active {
      color: red;
      border: 1px solid red;
    }

    span {
      border: none !important;
    }
  }
}
</style>