<template>
  <div>
    <van-nav-bar
      title="足迹"
      left-text="返回"
      :right-text="!isEdit? '编辑':'完成'"
      left-arrow
      @click-left="onClickLeft"
      @click-right="onClickRight"
    />
    <BgBox>
      <div class="sortingTitle">
        <div class="trackAll">足迹</div>
        <div class="delAll" v-if="isEdit" @click="allSelect">清空</div>
      </div>
      <van-empty v-if="trackCount.length==0" description="没有足迹" />
    </BgBox>
    <div class="menu-product">
      <div>
        <div class="clearfix m-pro-item" v-for="(item, index) in trackCount" :key="index">
          <van-swipe-cell>
            <!--  -->
            <div @click="goDetail(item.pid)">
              <div class="fl pro-img">
                <img class="auto-img" :src="item.smallImg" alt />
              </div>
              <div class="fl text">
                <div class="pro-text">
                  <div class="pro-time">{{item.time}}</div>
                  <div class="pro-name">{{item.name}}</div>
                  <div class="pro-enname">{{item.enname}}</div>
                </div>
              </div>
              <div class="fl price">￥{{item.price}}</div>
            </div>
            <template #right>
              <van-button square type="danger" @click="removeOne(index)" text="删除" />
            </template>
          </van-swipe-cell>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BgBox from "../components/BgBox.vue";
export default {
  data() {
    return {
      track: [],  

      isEdit: false,

      isAllChecked: false
    };
  },

  computed: {
    trackCount() {
      return this.$store.state.footprintData;
    }
  },
  methods: {
    //全删除
    allSelect() {

      if(this.trackCount.length==0){
         this.isEdit = !this.isEdit;
        this.$toast('没有足迹了');
        return
      }


      this.$dialog.confirm({
        title: "清空足迹",
        message: "清空后无法恢复",

      }).then(() => {

      this.$store.commit("delAllTrack");
      this.isEdit = !this.isEdit;

      }).catch(err => {
        
      })

    },

    //单个删除
    removeOne(index) {
      this.$store.commit("removeOne", index);
    },

    // 编辑
    onClickRight() {
      this.isEdit = !this.isEdit;
    },

    //返回
    onClickLeft() {
      this.$router.go(-1);
    },

    goDetail(pid) {
      this.$router.push({ name: "Detail", params: { pid } });
    }
  },
  components: {
    BgBox
  }
};
</script>

