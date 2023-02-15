<template>
  <div class="home">
    <van-nav-bar left-arrow>
      <template #left>
        <div class="home-nav">
          <div class="t1">{{ Greetings }}</div>
          <div class="t2 one_text">{{ userInfo.nickName }}</div>
        </div>
      </template>
      <template #right>
        <div class="home-search">
          <van-search placeholder="请输入搜索关键词" @focus="searchFocus"/>
        </div>
      </template>
    </van-nav-bar>
    <div class="home-content">
      <!-- 轮播图 -->
      <div class="banner-box">
        <van-swipe :autoplay="3000" indicator-color="white">
          <van-swipe-item v-for="(item, index) in bannerData" :key="index">
            <img class="auto_img" :src="item.bannerImg" alt="" />
          </van-swipe-item>
        </van-swipe>
      </div>
    </div>
    <!-- 商品 -->
    <div class="product-box">
      <div>
        <!-- 热卖推荐 -->
        <div class="pro-title-box">
          <span class="pro-title">热卖推荐</span>
        </div>
        <!-- 商品信息 -->
        <div class="product clearfix">
          <div
            class="pro-item fl"
            v-for="(item, index) in hotProduct"
            :key="index"
            @click="goDetail(item.pid)"
          >
            <div class="img-box">
              <img class="auto_img" :src="item.smallImg" alt="" />
              <!-- hot标签 -->
              <div class="hot">hot</div>
            </div>
            <div class="pro-info">
              <div class="pro-name one_text">{{ item.name }}</div>
              <div class="pro-enname one_text">{{ item.enname }}</div>
              <div class="pro-price">￥{{ item.price }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import "../assets/less/home.less";
// 默认导出组件
export default {
  // 组件名称
  name: "Home",
  // 页面数据
  data() {
    return {
      // 轮播图数据
      bannerData: [],
      // 热卖推荐数据
      hotProduct: [],
      // 动态时间
      Greetings: "",
      // 搜索关键词
      name: "",
      // 搜索的商品数据
      products: [],
      //用户信息
      userInfo: {},
    };
  },
  // 生命周期
  created() {
    // 获取轮播图数据
    this.getBannerData();
    // 获取热门产品
    this.getHotProduct();
    // 获取日期
    this.getDate();
     //获取用户信息
    this.getUserInfo();
  },
  methods: {
    // 动态获取时间
    getDate() {
      // 获取时间
      let hour = new Date().getHours();
      console.log(hour);
      if (hour < 6) {
        this.Greetings = "嘿,凌晨好!";
      } else if (hour < 9) {
        this.Greetings = "嘿,早上好!";
      } else if (hour < 12) {
        this.Greetings = "嘿,上午好!";
      } else if (hour < 14) {
        this.Greetings = "嘿,中午好!";
      } else if (hour < 18) {
        this.Greetings = "嘿 下午好!";
      } else if (hour < 23) {
        this.Greetings = "嘿 晚上好!";
      }
    },
    // 获取轮播图数据
    getBannerData() {
      this.$toast.loading({
        message: "加载中...",
        forbidClick: true,
        duration: 0,
      });
      // 发起注册请求
      this.axios({
        // 请求类型
        method: "GET",
        // 请求路径
        url: "/banner",
        // 请求参数
        params: {
          appkey: this.appkey,
        },
      }).then((result) => {
        this.$toast.clear();

        console.log(result.data.result);
        //  console.log(result.data.result[0].name);
        if (result.data.code == 300) {
          this.bannerData = result.data.result;
        }
      });
    },
    // 获取热卖推荐商品
    getHotProduct() {
      this.$toast.loading({
        message: "加载中...",
        forbidClick: true,
        duration: 0,
      });
      // 发起注册请求
      this.axios({
        // 请求类型
        method: "GET",
        // 请求路径
        url: "/typeProducts",
        // 请求参数
        params: {
          appkey: this.appkey,
          key: "isHot",
          value: 1,
        },
      }).then((result) => {
        this.$toast.clear();

        console.log(result);
        if (result.data.code == 500) {
          this.hotProduct = result.data.result;
        }
      });
    },
    search() {
      this.$toast.loading({
        message: "加载中....",
        forbidClick: true,
        duration: 0,
      });
      this.axios({
        method: "GET",
        url: "/search",
        params: {
          appkey: this.appkey,
          name: this.name,
        },
      }).then((result) => {
        console.log(result);
        this.$toast.clear();
        if (result.data.code == "Q001") {
          this.products = result.data.result;
        } else {
          this.$toast(result.data.msg);
        }
      });
    },

    // 查看商品详情
    goDetail(pid) {
      this.$router.push({ name: "Detail", params: { pid } });
    },
    // 搜索栏获取焦点
    searchFocus(){
      this.$router.push({name:"Search"});
    },
    //获取用户信息
    getUserInfo() {
      console.log(2222);
      let tokenString = localStorage.getItem("__tk");

      if (!tokenString) {
        //跳回登录页面
        this.$toast("请先登录");

        return this.$router.push({ name: "Login" });
      }

      this.$toast.loading({
        message: "加载中...",
        forbidClick: true,
        duration: 0,
      });

      this.axios({
        method: "GET",
        url: "/findMy",
        params: {
          appkey: this.appkey,
          tokenString,
        },
      })
        .then((result) => {
          this.$toast.clear();
          console.log(result);
          if (result.data.code == 700) {
            console.log(9999);
            //token检验无效,则跳到登录页面
            this.$router.push({ name: "Login" });
          } else if (result.data.code == "A001") {
            this.userInfo = result.data.result[0];
          }
        })
        .catch((err) => {
          this.$toast.clear();
        });
    },
  },
};
</script>
<style lang="less" scoped>
</style>
