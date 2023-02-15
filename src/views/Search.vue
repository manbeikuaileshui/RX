<template>
  <div class="search">
    <van-nav-bar left-text="返回" left-arrow @click-left="onClickLeft">
      <template #right>
        <div class="home-search">
          <van-search
            v-model="name"
            placeholder="请输入搜索关键词"
            ref="search"
            @search="search"
          />
        </div>
        <div class="qx" @click="onClickLeft">取消</div>
      </template>
    </van-nav-bar>
    <BgBox>
      <div class="clearfix">
        <div
          class="like-item fl"
          v-for="(item, index) in products"
          :key="index" @click="goDetail(item.pid)">
          <ProductItem :item="item"></ProductItem>
        </div>
      </div>
    </BgBox>
  </div>
</template>
<script>
import BgBox from "../components/BgBox.vue";
import ProductItem from "../components/ProductItem.vue";
export default {
  name: "Search",
  //   注册
  components: {
    BgBox,
    ProductItem,
  },
  data() {
    return {
      // 搜索关键词
      name: "",
      // 搜索的商品数据
      products: [],
    };
  },
  created() {},
  methods: {
    onClickLeft() {
      this.$router.back(-1);
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
    goDetail(pid) {
      this.$router.push({ name: "Detail", params: { pid } });
    },
  },
};
</script>
<style lang="less" scope>
.search {
  /deep/ .van-nav-bar__right {
    width: 70%;
  }
  /deep/ .home-search {
    width: 100%;
  }
   .home-search .van-search {
    padding: 0;
    border-radius: 17px;
    overflow: hidden;
  }
  .like-item{
    width: calc(~"100% / 3 - 10px + 10px / 3");
    margin-right: 10px;
    margin-bottom: 10px;
    &:nth-child(3n){
        margin-right: 0px;
    }
  }
  .qx{
    width: 50px;
  }
}
</style>
