<template>
  <div class="menu">
    <div class="menu-nav">
      <!-- 搜索框 -->
      <div class="menu-search">
        <van-search placeholder="请输入搜索关键词" @focus="searchFocus"/>
      </div>
      <!-- 分类菜单 -->
      <div class="menu-option">
        <div class="menu-item" v-for="(item, index) in menuOption" :key="index" 
          @click="toggleMenu(index,item.type)"
        >
          <!-- 一个选项 -->
          <div class="m-item">
            <!-- 图标 -->
            <div class="m-icon">
              <img
                class="auto_img"
                :src="menuIndex == index ? item.activeIcon : item.inactiveIcon"
                alt=""
              />
            </div>
            <!-- 标题 -->
            <div class="m-text" :class="{active :menuIndex == index}">{{ item.title }} 
            </div>
          </div>
        </div>
      </div>
    </div>
    <!-- 商品信息 -->
    <div class="menu-product">
      <div class="m-pto-item clearfix" v-for="(item,index) in productData" :key="index" @click="goDetail(item.pid)">
        <div class="pro-img fl">
          <img :src="item.smallImg" alt="" class="auto_img">
        </div>
        <div class="text fl">
          <div class="pro-text">
            <div class="pro-name">{{item.name}}</div>
          <div class="pro-enname">{{item.enname}}
          </div>
          </div>
        </div>
        <div class="price fl">
          ￥{{item.price}}
        </div>
      </div>
      
    </div>
  </div>
</template>
<script>
import "../assets/less/menu.less";
export default {
  // 组件名称
  name: "Menu",
  data() {
    return {
      menuIndex: 0,
      menuOption: [
        {
          title: "推荐",
          inactiveIcon: require("../assets/images/icons_09.gif"),
          activeIcon: require("../assets/images/icons_21.gif"),
        },
        {
          title: "拿铁",
          inactiveIcon: require("../assets/images/icons_05.gif"),
          activeIcon: require("../assets/images/icons_19.gif"),
        },
        {
          title: "咖啡",
          inactiveIcon: require("../assets/images/icons_03.gif"),
          activeIcon: require("../assets/images/icons_18.gif"),
        },
        {
          title: "瑞纳冰",
          inactiveIcon: require("../assets/images/icons_07.gif"),
          activeIcon: require("../assets/images/icons_20.gif"),
        },
      ],
      productData:[],
    };
  },
  
  methods: {
    // 点击切换效果
    toggleMenu(index,type){
      if(this.menuIndex == index){
          return;
      }
      this.menuIndex = index;
      this.getProductByType(type);
    },
    // 获取商品类型
    getTypes() {
      // 发起注册请求
      this.axios({
        method: "GET",
        url: "/type",
        // 请求参数
        params: {
          appkey: this.appkey,
        },
      }).then((result) => {
        console.log(result);
        if (result.data.code == 400) {
          let data = result.data.result;
          console.log(data);
          data.unshift({
            type: "isHot",
            typeDesc: "推荐",
          });
          this.menuOption.map(v => {
            for (let i = 0; i < data.length; i++) {
              if (v.title == data[i].typeDesc) {
                v.type = data[i].type;
                break;
              }
            }
          });
          let type = this.menuOption[this.menuIndex].type;
          this.getProductByType(type);
        }
      });
    },
    // 根据商品类型获取数据(拿到所有的数据，才能对数据的类型进行分类)
    getProductByType(type) {
      // 请求参数
      let params ={
        appkey:this.appkey,
      };
      if(type=="isHot"){
        params.key = "isHot";
        params.value = 1;
      }else{
        params.key = "type";
        params.value = type;
      }
      // 发起请求
      this. axios({
        method:"GET",
         url: "/typeProducts",
        // 请求参数
        params,
      }).then((result) => {
        console.log(result.data.result);
        if(result.data.code == 500){
          this.productData= result.data.result;
        }
      });
    },
    // 搜索栏获取焦点
    searchFocus(){
      this.$router.push({name:"Search"});
    },
    // 查看商品详情
    goDetail(pid) {
      this.$router.push({ name: "Detail", params: { pid } });
    },
  },
  created(){
    this.getTypes();
  },
};
</script>
<style lang="less" scoped></style>
