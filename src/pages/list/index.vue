<template>
  <view class="list">
    <!-- 筛选 -->
    <view class="filter">
      <text class="active">综合</text>
      <text>销量</text>
      <text>价格</text>
    </view>
    <!-- 商品列表 -->
    <view class="goods">
      <view class="item" @click="goDetail"
      v-for="item in goodslist" :key="item.goods_id"
      >
        <!-- 商品图片 -->
        <image class="pic" :src="item.goods_small_logo"></image>
        <!-- 商品信息 -->
        <view class="meta">
          <view class="name">{{item.goods_name}}</view>
          <view class="price">
            <text>￥</text>{{item.goods_price}}<text>.00</text>
          </view>
        </view>
      </view>
      <!-- 没有更多了 -->
      <view v-show="isend">客官 没有更多了哦~~</view>
    </view>
     
  </view>
</template>

<script>
  export default {
    data(){
      return{
        params:"" ,//{query: "飞机"}
        goodslist:[],//商品列表,
        pagenum:1,//当前的页数
        pagesize:20,//每页显示几条
        total:0,
        isend:false


      }
    },
  onLoad(params){
      console.log(params);//打印出来的params是个对象{query: "飞机"}
      this.params=params
      this.getGoodsList()
    },
    methods: {
      goDetail () {
        uni.navigateTo({
          url: '/pages/goods/index'
        })
      },
      //求数据
     async getGoodsList(){
       let res= await this.http({
          url:"/api/public/v1/goods/search",
          data:{
            query:this.params.query,//大米
            pagenum:this.pagenum,// 页码
            pagesize:this.pagesize // 每页显示的条数
          }
        })
        console.log("梁山",res);
        this.goodslist=[...this.goodslist,...res.message.goods]
        this.total=res.message.total // 61
      },
      //监听上拉加载的函数
      onReachBottom(){
        if (this.goodslist.length === this.total) {
          this.isend=true
          return;
          
        }
        // 上拉到银锭程度的时候请第2页数据 然后再第3页数据  直到求完为止
        this.pagenum+=1//this.pagenum=this.pagenum+1
        this.getGoodsList()

      }
    
    }
  }
</script>

<style scoped lang="scss">
  .list{
    padding-top: 100rpx;
  }
  .filter {
    display: flex;
    height: 96rpx;
    line-height: 96rpx;
    border-bottom: 1rpx solid #ddd;

    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 9999;
    background-color: #fff;

    text {
      flex: 1;
      text-align: center;
      font-size: 30rpx;
      color: #333;

      &.active {
        color: #ea4451;
      }
    }
  }
  

  .item {
    display: flex;
    padding: 30rpx 20rpx 30rpx 0;
    margin-left: 20rpx;
    border-bottom: 1rpx solid #eee;

    &:last-child {
      border-bottom: none;
    }

    .pic {
      width: 200rpx;
      height: 200rpx;
      margin-right: 30rpx;
    }

    .meta {
      flex: 1;
      font-size: 27rpx;
      color: #333;
      position: relative;
    }

    .name {
      width: 100%;
      overflow: hidden;
      text-overflow: ellipsis;
      display: -webkit-box;
      -webkit-line-clamp: 2;
      -webkit-box-orient: vertical;
    }

    .price {
      position: absolute;
      bottom: 0;

      color: #ea4451;
      font-size: 33rpx;

      text {
        font-size: 22rpx;
      }
    }
  }
</style>
