<template>
  <div class="login">
    <!-- 头部 -->
    <div class="nav">
      <!-- 左边 -->
      <div class="nav-left">
        <img class="logo" src="../assets/images/home_active.png" alt="" />
        <span>Luckin Coffee</span>
      </div>

      <!-- 右边边 -->
      <div class="nav-right">先逛一逛</div>
    </div>

    <!-- 大图 -->
    <div class="big-img">
      <img src="../assets/images/home_active.png" alt="" />
    </div>

    <div class="content">
      <!-- 登录 -->
      <van-form>
        <van-field
          v-model="login.phone"
          name="手机号"
          label="手机号"
          placeholder="手机号"
        />

        <!-- closed-eye eye-o   -->
        <!--    当我showEye是true的时候我就选 eye-o 否则是closed-eye -->
        <!--  :right-icon="showEye?'eye-o':'closed-eye'" -->
        <van-field
          v-model="login.password"
          :type="showEye ? 'text' : 'password'"
          name="密码"
          label="密码"
          :right-icon="showEye ? 'eye-o' : 'closed-eye'"
          placeholder="密码必须为字母开头"
          @click-right-icon="toggleShowEye"
        />

        <p class="forget" @click="toForget">忘记密码?</p>
        <div style="margin: 16px;">
          <van-button round block type="info" color="#0c34ba" @click="singin">
            登录
          </van-button>
        </div>
        <!-- 注册 -->
        <div style="margin: 16px;" class="res">
          <van-button
            round
            block
            type="info"
            color="#ffffff"
            native-type="submit"
            @click="showPopup"
          >
            注册
          </van-button>
        </div>
      </van-form>
    </div>

    <!-- 注册 -->
    <div class="regsiter">
      <van-popup
        v-model="show"
        closeable
        position="bottom"
        :style="{ height: '50%' }"
      >
        <h1 class="title">注册</h1>
        <!-- 登录 -->
        <van-form>
          <van-field
            v-model="register.phone"
            name="手机号"
            label="手机号"
            placeholder="手机号"
          />

          <!-- closed-eye eye-o   -->
          <!--    当我showEye是true的时候我就选 eye-o 否则是closed-eye -->
          <!--  :right-icon="showEye?'eye-o':'closed-eye'" -->
          <van-field
            v-model="register.password"
            :type="showEye ? 'text' : 'password'"
            name="密码"
            label="密码"
            :right-icon="showEye ? 'eye-o' : 'closed-eye'"
            placeholder="密码必须为字母开头"
            @click-right-icon="toggleShowEye"
          />

          <van-field
            v-model="register.nickName"
            name="昵称"
            label="昵称"
            placeholder="昵称"
          />
          <div style="margin: 50px 16px 0 16px;">
            <van-button
              round
              block
              type="info"
              color="#0c34ba"
              @click="sendPassword"
            >
              注册
            </van-button>
          </div>
          <!-- 注册 -->
        </van-form>
      </van-popup>
    </div>
  </div>
</template>
<script>
import '../assets/less/login.less'

// @就是表示src 这个文件
import { validForm } from '@/assets/validForm'
export default {
  data() {
    return {
      login: {
        phone: '',
        password: '',
      },
      //   注册的账号密码和昵称
      register: {
        phone: '',
        password: '',
        nickName: '',
      },
      // 控制眼睛的显示和隐藏
      showEye: false,
      show: false,
    }
  },
  // 方法
  methods: {
    toForget(){
      this.$router.push({path:"/forget"})
    },
    showPopup() {
      this.show = true
    },
    toggleShowEye() {
      // 取反
      this.showEye = !this.showEye
    },

    // 注册
    sendPassword() {
      console.log(this.register)

      // 做一个校正  判断我们出入的手机号和密码 和昵称 是否符合规则
      //构造表单验证信息  注册正则
      let o = {
        phone: {
          value: this.register.phone, ///我们传递的一个值
          errorMsg: '手机号格式不正确', // 就是不符合规则时候弹出的信息
          reg: /^1[3-9]\d{9}$/, /// 正则
        },
        password: {
          value: this.register.password,
          errorMsg: '密码由数字字母下划线组合(6-16字符)',
          reg: /^[A-Za-z]\w{5,15}$/,
        },
        nickName: {
          value: this.register.nickName,
          errorMsg: '昵称由字母数字下划线汉字组合(1-10字符)',
          reg: /^[\w\u4e00-\u9fa5]{1,10}$/,
        },
      }

      let isPass = validForm.valid(o)

      // isPass 判断是否 校验成功  如果是正确 就是true 错误就是false

      console.log('isPass', isPass)

      //  !false = true
      if (!isPass) {
        // 终止后面代码的请求
        return
      }

      // console.log(11111111);

      // 请求

      this.axios({
        method: 'post',
        url: '/register',
        data: {
          appkey: this.appkey,
          nickName: this.register.nickName,
          password: this.register.password,
          phone: this.register.phone,
        },
      })
        .then((res) => {
          console.log('注册后输出的数据', res)

          // 102  就是手机号被注册  要提示给用户
          // 100 是注册成功
          if (res.data.code == 102) {
            this.$toast('手机号被注册')
          } else if (res.data.code == 100) {
            this.$toast('注册成功')

            // 把注册成功的手机号和密码赋值到登录的输入框里面
            this.login.phone = this.register.phone
            this.login.password = this.register.password

            // 关闭 那个弹框
            this.show = false
          }
        })
        .catch((err) => {
          console.log('注册后输出的数据err', err)
        })
    },
    // 登录
    singin() {
      // 做一个校正  判断我们出入的手机号和密码 和昵称 是否符合规则
      //构造表单验证信息  注册正则
      let o = {
        phone: {
          value: this.login.phone, ///我们传递的一个值
          errorMsg: '手机号格式不正确', // 就是不符合规则时候弹出的信息
          reg: /^1[3-9]\d{9}$/, /// 正则
        },
        password: {
          value: this.login.password,
          errorMsg: '密码由数字字母下划线组合(6-16字符)',
          reg: /^[A-Za-z]\w{5,15}$/,
        },
      }

      let isPass = validForm.valid(o)

      if (!isPass) {
        return
      }

      this.axios({
        method: 'post',
        url: '/login',
        data: {
          appkey: this.appkey,
          password: this.login.password,
          phone: this.login.phone,
        }
      }).then(res=>{
        console.log('登录的信息',res);
        if(res.data.code==200) {
          this.$toast('登录成功')
          //                  字段(自定义)
          localStorage.setItem("__tk",res.data.token)

          // 可以跳转到首页  
          this.$router.push({path:"/home"})

          // 可以返回上一页
          // this.$router.go(-1)

        }else  if(res.data.code==202) {
          this.$toast('账号密码不正确')
        }
      }).catch(err=>{
        console.log(err);
      })
    },
  },
}
</script>

<style lang="less" scoped></style>
