<template>
    <div>
        <nav-bar title="商品详情"/>
        <div class="all-layout">

        <div class="outer-swiper">
            <div class="swiper">
                <my-swipe :imgs="imgs"/>
            </div>
        </div>
        <div class="product-desc">
            <ul>
                <li><span class="product-desc-span">
                    {{info.title}}
                </span></li>
                <li class="price-li">市场价：
                    <s>￥{{info.market_price}}</s> 销售价：<span>￥{{info.sell_price}}</span></li>
                <li class="number-li">购买数量：<span @click="less">-</span><span>{{buyNum}}</span><span @click="add">+</span></li>
                <li>
                    <mt-button type="primary">立即购买</mt-button>
                    <mt-button type="danger" @click="addShopcart">加入购物车</mt-button>
                </li>
            </ul>
        </div>

        <!-- 过度效果内置组件 -->
        <transition name="ball"
        @after-enter='afterEnter'
        >
            <div class="ball" v-if="isShow"></div>
        </transition>

        <div class="product-props">
            <ul>
                <li>商品参数</li>
                <li>商品货号：{{info.goods_no}}</li>
                <li>库存情况：{{info.stock_quantity}}件</li>
                <li>上架时间：{{info.add_time}}</li>
            </ul>
        </div>
        <div class="product-info">
            <ul>
                <li>
                    <mt-button type="primary" @click="goGoodsInfo" size="large" plain >图文介绍</mt-button>
                </li>
                <li>
                    <mt-button type="danger" @click="goGoodsComment" size="large" plain >商品评论</mt-button>
                </li>
            </ul>
        </div>
        </div>
    </div>
</template>
<script>
import GoodsTools from '@/GoodsTools.js'
import {mapActions} from 'vuex'
export default {
  data () {
    return {
      imgs: [],
      info: {},
      isShow: false,
      goodsId: +this.$route.query.id + 1,
      buyNum: 1
    }
  },
  methods: {
    ...mapActions([
      'addNumByAct',
      'changeNumByAct'
    ]),
    afterEnter () {
      this.isShow = false
      GoodsTools.addShopcart({
        id: this.info.id,
        num: this.buyNum
      })
      this.addNumByAct(this.buyNum)
    },
    addShopcart () {
      this.isShow = true
    },
    add () {
      if (this.buyNum == this.info.stock_quantity) {
        return
      }
      this.buyNum++
    },
    less () {
      if (this.buyNum <= 1) {
        return
      }
      this.buyNum--
    },
    goGoodsInfo () {
      this.$router.push({
        name: 'NewsDetail',
        params: {
          id: this.$route.query.id
        }
      })
    },
    goGoodsComment () {
      this.$router.push({
        name: 'GoodsComment',
        query: {
          id: this.$route.query.id
        }
      })
    }
  },
  created () {
    //   数据不够，复用数据
    if (this.goodsId > 4) {
      this.goodsId = 4
      console.log('没有数据，借用')
    }
    //  over

    let imgReq = this.$axios.get(`../../../static/req/shop/detail/img${this.goodsId}.json`)
    let infoReq = this.$axios.get(`../../../static/req/shop/detail/info${this.goodsId}.json`)

    this.$axios.all([imgReq, infoReq])
      .then(this.$axios.spread(
        (imgRes, infoRes) => {
          this.imgs = imgRes.data.message
          this.info = infoRes.data.message[0]
        }
      ))
  }
}
</script>
<style scoped>
/*元素被移除前默认有一个透明度1的显示的*/
.ball-leave {
    opacity: 0;
}
.ball-enter-active {
    animation: bounce-in 1s;
}

@keyframes bounce-in {
    0% {
        transform: translate3d(0, 0, 0);
    }
    50% {
        transform: translate3d(140px, -50px, 0);
    }
    75% {
        transform: translate3d(160px, 0px, 0);
    }
    100% {
        transform: translate3d(140px, 300px, 0);
    }
}

.swiper {
    border: 1px solid rgba(0, 0, 0, 0.2);
    margin: 8px;
    width: 95%;
    border-radius: 15px;
    overflow: hidden;
}

.outer-swiper,
.product-desc,
.product-props,
.product-info {
    border-radius: 5px;
    border: 1px solid rgba(0, 0, 0, 0.2);
    margin: 3px;
}

/*请ulpadding*/

.outer-swiper ul,
.product-desc ul,
.product-props ul,
.product-info ul {
    padding: 0;
}

.product-desc ul li,
.product-props ul li,
.product-info ul li {
    list-style: none;
    font-size: 15px;
    color: rgba(0, 0, 0, 0.5);
    margin-top: 8px;
}

.product-desc ul >:nth-child(1) span {
    color: blue;
    font-size: 22px;
    font-weight: bold;
}

.product-desc ul >:nth-child(1) {
    border-bottom: 1px solid rgba(0, 0, 0, 0.5);
}

.product-desc ul,
.product-info ul,
.product-props ul {
    padding-left: 10px;
}

.price-li span {
    color: red;
    font-size: 25px;
}

.price-li s {
    margin-right: 16px;
}

/*加减*/

.number-li span {
    display: inline-block;
    border: 2px solid rgba(0, 0, 0, 0.1);
    width: 25px;
}

/*商品参数*/

.product-props ul >:nth-child(1) {
    text-align: left;
}

.product-props ul:not(:nth-child(1)) {
    margin-left: 40px;
}

.product-info ul {
    margin-bottom: 70px;
    padding: 0 5;
}

.number-li span {
    text-align: center;
}

.number-li >:nth-child(2) {
    width: 40px;
}

.ball {
    border-radius: 50%;
    background-color: red;
    width: 24px;
    height: 24px;
    position: absolute;
    top: 440px;
    left: 120px;
    display: inline-block;
    z-index: 9999;
}
</style>
