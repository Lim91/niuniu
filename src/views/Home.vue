<template>
  <div class="home">
    <van-nav-bar>
      <template #left>
        <div class="home-nav">
          <div class="t1">{{ nowTime }}</div>
          <div class="t2">Lim</div>
        </div>
      </template>
      <template #right>
        <div class="home-search">
          <van-search placeholder="输入商品名称" @focus="onSearch" />
        </div>
      </template>
    </van-nav-bar>

    <div class="home-content">
      <!-- 轮播图 -->
      <div class="banner-box">
        <van-swipe @change="changeCurrentIndex" :autoplay="3000" loop>
          <van-swipe-item v-for="(item, index) in bannerData" :key="index">
            <img
              class="auto-img"
              :src="item.bannerImg"
              alt=""
              @click="goDetail(item.pid)"
            />
            <div class="banner-desc">
              <p>{{ item.name }}</p>
            </div>
          </van-swipe-item>
          <template #indicator>
            <div class="index-box">
              <div
                :class="{ active: index == currentIndex }"
                v-for="(item, index) in bannerData"
                :key="index"
              ></div>
            </div>
          </template>
        </van-swipe>
      </div>

      <!-- 商品 -->
      <div class="product-box">
        <div>
          <div class="clearfix pro-title-box">
            <span class="pro-title">热卖推荐</span>
          </div>

          <div class="products clearfix">
            <div
              class="pro-item fl"
              v-for="(item, index) in hotProduct"
              :key="index"
              @click="goDetail(item.pid)"
            >
              <div class="img-box">
                <img class="auto-img" :src="item.smallImg" />
                <!-- hot标签 -->
                <div class="hot">hot</div>
              </div>
              <div class="pro-info">
                <div class="pro-name one-text">{{ item.name }}</div>
                <div class="pro-enname one-text">{{ item.enname }}</div>
                <div class="pro-price">￥{{ item.price }}</div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "../assets/less/home.less";
export default {
  name: "Home",
  data() {
    return {
      //当前轮播图片索引
      currentIndex: 0,

      //轮播数据
      bannerData: [],

      //热卖商品数据
      hotProduct: [],
    };
  },

  computed: {
    nowTime: () => {
      let hour = new Date().getHours();
      if (hour >= 18 && hour <= 6) {
        return "晚上好";
      } else if (hour > 6 && hour <= 10) {
        return "早上好";
      } else if (hour > 10 && hour <= 12) {
        return "中午好";
      } else {
        return "下午好";
      }
    },
  },

  created() {
    //获取轮播图数据
    this.getBannerData();

    //获取热卖推荐商品
    this.getHotProduct();
  },

  methods: {
    //修改轮播图片索引
    changeCurrentIndex(index) {
      this.currentIndex = index;
    },

    //获取轮播图数据
    getBannerData() {
      this.$toast.loading({
        message: "加载中...",
        forbidClick: true,
        duration: 0,
      });

      //发起注册请求
      this.axios({
        //请求类型
        method: "GET",
        //请求路径
        url: "/banner",

        //GET请求参数, object
        params: {
          appkey: this.appkey,
        },
      })
        .then((result) => {
          this.$toast.clear();

          console.log("result ==> ", result);

          if (result.data.code == 300) {
            this.bannerData = result.data.result;
          }
        })
        .catch((err) => {
          this.$toast.clear();

          console.log("err ==> ", err);
        });
    },

    onSearch() {
      this.$router.push({ name: "Search" });
    },

    //获取热卖推荐商品
    getHotProduct() {
      this.$toast.loading({
        message: "加载中...",
        forbidClick: true,
        duration: 0,
      });

      //发起注册请求
      this.axios({
        //请求类型
        method: "GET",
        //请求路径
        url: "/typeProducts",

        //GET请求参数, object
        params: {
          appkey: this.appkey,
          key: "isHot",
          value: 1,
        },
      })
        .then((result) => {
          this.$toast.clear();

          console.log("result ==> ", result);

          if (result.data.code == 500) {
            this.hotProduct = result.data.result;
          }
        })
        .catch((err) => {
          this.$toast.clear();

          console.log("err ==> ", err);
        });
    },

    //查看商品详情页面
    goDetail(pid) {
      this.$router.push({ name: "Detail", params: { pid } });
    },
  },
};
</script>