<template>
  <div class="p-news-search">
    <header class="m-box m-aln-center m-pos-f m-main m-bb1 m-head-top">
      <div class="m-box m-flex-grow0 m-flex-shrink0 m-aln-center">
        <svg class="m-style-svg m-svg-def" @click="goBack">
          <use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#base-back"></use>
        </svg>
      </div>
      <div class="m-box m-flex-grow2 m-flex-shrink2 m-aln-center m-head-top-title">
        <div class="m-search-box">
          <form action="#" onsubmit="return false">
            <input type="search" v-model='keyword' placeholder="搜索" @input="searchNewsByKey">
          </form>
        </div>
      </div>
      <div class="m-box m-flex-grow0 m-flex-shrink0 m-aln-center m-justify-end">
          <a @click.prevent.stop='goBack'>取消</a>
      </div>
    </header>
    <jo-load-more
    ref='loadmore'
    @onRefresh='onRefresh'
    @onLoadMore='onLoadMore'
    :auto-load="false"
    :showBottom="list.length > 0"
    style="padding-top: 0.9rem">
      <news-item
      v-for='news in list'
      v-if='news.id'
      :key='news.id'
      :news='news'
      />
    </jo-load-more>
  </div>
</template>
<script>
import _ from "lodash";
import { searchNewsByKey } from "@/api/news.js";
import newsItem from "./components/newsItem.vue";
export default {
  name: "news-search",
  components: {
    newsItem
  },
  data() {
    return {
      keyword: "",
      list: []
    };
  },
  computed: {
    after() {
      const len = this.list.length;
      return len > 0 ? this.list[len - 1].id : 0;
    }
  },
  methods: {
    searchNewsByKey: _.throttle(function() {
      this.keyword &&
        searchNewsByKey(this.keyword).then((list = []) => {
          this.list = list;
        });
    }, 1e3),
    onRefresh(callback) {
      searchNewsByKey(this.keyword, 15).then(list => {
        this.list = list;
        callback(list.length < 15);
      });
    },
    onLoadMore(callback) {
      searchNewsByKey(this.keyword, 15, this.after).then(list => {
        this.list = [...this.list, ...list];
        callback(list.length < 15);
      });
    }
  }
};
</script>
<style lang="less">
.p-news-search {
  .m-head-top-title {
    padding: 0 20px;
    .m-search-box {
      width: 100%;
    }
  }

  .jo-loadmore-head {
    top: 90px;
  }
}
</style>
