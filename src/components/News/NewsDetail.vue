<template>
  <div>
      <nav-bar :title="myTitle"></nav-bar>
      <div class="news-title">
          <p>{{newsInfo.title}}</p>
          <div>
              <span>{{newsInfo.click}}次点击</span>
              <span>分类：民生经济</span>
              <span>添加时间：{{newsInfo.add_time}}</span>
          </div>
      </div>
      <div id="news-content">
          <div class="news-content-c" v-html="newsInfo.content"></div>
      </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      newsInfo: [],
      myTitle: ''
    }
  },
  created () {
    let { id } = this.$route.params
    let url = '../../../static/req/newContent/newContent' + id + '.json'
    this.$axios.get(url).then(res => {
      this.newsInfo = res.data.message[0]
    })
  },
  beforeRouteEnter (to, from, next) {
    // ...
    let title = '详情'
    if(from.name === 'NewsList'){
      title = '新闻详情'
    }else if (from.name === 'GoodsDetail'){
      title = '商品图文信息'
    }
    next(vm => {
      vm.myTitle = title
    })
  }
}
</script>

<style>
.news-title p{
    color: #0a87f8;
    font-size: 20px;
    font-weight: bold;
}
.news-title span{
    margin-right: 30px;
}
.news-title {
    margin-top: 5px;
    border-bottom: 1px solid rgba(0, 0, 0, 0.2);
}
#news-content{
    overflow-y: auto;
    height: calc(100vh - 180px);
}
#news-content img{
    width: 100%;
}
#news-content .news-content-c{
    padding: 10px 5px
}
#news-content .news-content-c img{
    width: 100px;
}
</style>
