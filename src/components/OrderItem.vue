<template>
    <div class="order-item">
        <div class="cell-item-box">
            <div class="pro-info-box">
                <!-- 商品图片 -->
                <div class="pro-img fl">
                    <img :src="item.small_img || item.smallImg" class="auto-img" alt="">
                </div>
                <!-- 商品的信息（文字） -->
                <div class="pro-info fl">
                    <div class="pro-info-text">
                        <div class="names clearfix">
                            <div class="pro-name">{{item.name}}</div>
                            <div class="pro-rule">{{item.rule}}</div>
                        </div>
                        <div class="pro-enname">{{item.enname}}</div>
                    </div>
                    <!-- 商品的信息（价格和数量） -->
                    <div class="pro-count-box">
                            <div class="pro-price fl">￥{{item.price}}</div>
                            <div class="pro-step-box fl">X{{item.count}}</div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script>
export default {
    name:"OrderItem",
    props:{
        item:{
            type:Object,
            default(){
                return{
                    name:"名称",
                    rule:"规格",
                    enname:"英文名称",
                    price:0,
                    count:1
                };
            }
        }
    }
}
</script>