<template>
    <div class="product-item bg-contents">
        <div class="">
            <img :src="item.smallImg" alt="" class="auto_img">
        </div>
        <div class="pro-name one_text">{{item.name}}</div>
        <div class="pro-enname one_text">{{item.enname}}</div>
        <div>
            <div class="pro-price one_text">￥{{item.price}}</div>
        </div>
        
    </div>
</template>
<script>
export default {
    name:"ProductItem",
    // 传值
    props:{
        item:{
            type:Object,
            default(){
                return{};
            }
        }
    }
}
</script>
<style lang="less" scope>
        .product-item{
            
            .pro-name{
                margin-top: 10px;
                font-size: 14px;
            }
            .pro-enname{
                margin-top: 5px;
                margin-bottom: 10px;
                font-size: 12px;
                font-weight: bold;
            }
            .pro-price{
                font-size: 14px;
                font-weight: bold;
                color: #0c34ba;
            }
        }
        .one_text{
            text-align: center;
        }
         .bg-contents{
            margin: 0 10px;
            padding: 10px;
            position: relative;
            // top: -20px;
            background-color: #fff;
            // border-top-left-radius: 15px;
            // border-top-right-radius: 15px;
        }
</style>
