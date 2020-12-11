<template>
  <div class="search">
    <div class="search-box">
      <van-nav-bar
        @click-left="goback"
        @click-right="searchClear"
        right-text="取消"
        left-arrow
        left-text="返回"
      >
        <template #title>
          <van-search
            @input="searchProductData"
            placeholder="拿铁、摩卡、瑞纳冰"
            shape="round"
            ref="search"
          />
        </template>
      </van-nav-bar>
    </div>

    <div v-if="searchData.length == 0">
      <van-empty
        class="custom-image"
        image="https://img.yzcdn.cn/vant/custom-empty-image.png"
        description="请输入要搜索的商品名称"
      />
    </div>

    <div class="menu-product">
      <div
        class="product-list"
        v-for="(item, index) in searchData"
        :key="index"
        :class="{ 'not-first': index > 0 }"
        @click="productDetail(item.pid)"
      >
        <div class="pro-box">
          <div class="pro-img">
            <img :src="item.smallImg" class="auto-img" />
          </div>
          <div class="pro-info">
            <div class="pro-text fl">
              <div class="ch-name">{{ item.name }}</div>
              <div class="ch-name">{{ item.enname }}</div>
            </div>
            <div class="pro-price fr">￥{{ item.price }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import "../assets/less/search.less";
export default {
  data() {
    return {
      searchData: [],
    };
  },

  created() {
    this.$nextTick(() => {
      //获取搜索框
      let searchIpt = this.$refs.search.querySelector('[type="search"]');

      //获取焦点
      searchIpt.focus();
    });
  },

  methods: {
    goback() {
      this.$router.go(-1);
    },

    //搜索商品
    searchProductData(value) {
      this.$toast.loading({
        message: "加载中...",
        forbidClick: true,
        duration: 0,
        loadingType: "spinner",
      });
      this.axios({
        method: "GET",
        url: "search",
        //如果get请求，参数需要放在params, 如果是post请求,参数需要放在data
        params: {
          appkey: this.appkey,
          name: value,
        },
      })
        .then((result) => {
          this.$toast.clear();

          if (result.data.code == "Q001") {
            this.searchData = result.data.result;
          }
        })
        .catch((err) => {
          this.$toast.clear();
        });
    },

    //清空列表
    searchClear() {
      this.searchData = [];
    },

    //商品详情
    productDetail(pid) {
      this.$router.push({ name: "Detail", params: { pid } });
    },
  },
};
</script>
