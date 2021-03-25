<template>
  <div class="profile-page">
    <div class="user-info">
      <div class="container">
        <div class="row">
          <div class="col-xs-12 col-md-10 offset-md-1">
            <img class="user-img" :src="user.image" />

            <h4>{{ user.username }}</h4>
            <p>
              {{ user.bio }}
            </p>
            <button class="btn btn-sm btn-outline-secondary action-btn">
              <i class="ion-plus-round"></i>
              &nbsp; Follow {{ user.username }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div class="row">
        <div class="col-xs-12 col-md-10 offset-md-1">
          <div class="articles-toggle">
            <ul class="nav nav-pills outline-active">
              <li class="nav-item">
                <a
                  class="nav-link"
                  :class="{
                    active: tab === 'my_articles',
                  }"
                  @click="getArticles"
                  >My Articles</a
                >
              </li>
              <li class="nav-item">
                <a
                  class="nav-link"
                  :class="{
                    active: tab === 'favorited_articles',
                  }"
                  @click="getFavoriteArticle"
                  >Favorited Articles</a
                >
              </li>
            </ul>
          </div>

          <div
            class="article-preview"
            v-for="(item, index) in articleList"
            :key="index"
          >
            <div class="article-meta">
              <nuxt-link
                :to="{
                  name: 'profile',
                  params: {
                    username: item.author.username,
                  },
                }"
              >
                <img :src="item.author.image" />
              </nuxt-link>

              <div class="info">
                <nuxt-link
                  class="author"
                  :to="{
                    name: 'profile',
                    params: {
                      username: item.author.username,
                    },
                  }"
                >
                  {{ item.author.username }}
                </nuxt-link>

                <span class="date">{{
                  item.createdAt | date("MMM DD, YYYY")
                }}</span>
              </div>
              <button
                class="btn btn-outline-primary btn-sm pull-xs-right"
                :class="{
                  active: item.favorited,
                }"
                @click="onFavorite(item)"
              >
                <i class="ion-heart"></i> {{ item.favoritesCount }}
              </button>
            </div>
            <nuxt-link
              class="preview-link"
              :to="{
                name: 'article',
                params: { slug: item.slug },
              }"
            >
              <h1>{{ item.title }}</h1>
              <p>{{ item.description }}</p>
              <span>Read more...</span>
              <ul class="tag-list">
                <li
                  class="tag-default tag-pill tag-outline"
                  v-for="tag in item.tagList"
                  :key="tag"
                >
                  {{ tag }}
                </li>
              </ul>
            </nuxt-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { getArticles, addFavorite, deleteFavorite } from "@/api/article";
import { mapState } from "vuex";
export default {
  middleware: "authenticated",
  name: "UserProfile",
  data() {
    return {
      tab: "my_articles",
    };
  },
  async asyncData({ params }) {
    const { data } = await getArticles({ author: params.username });

    return {
      articleList: data.articles,
      user: data.articles[0].author || {},
    };
  },
  computed: {
    ...mapState(["user"]),
  },
  methods: {
    async getFavoriteArticle() {
      this.tab = "favorited_articles";
      const { data } = await getArticles({
        favorited: this.$router.currentRoute.params.username,
      });

      this.articleList = data.articles;
    },
    async getArticles() {
      this.tab = "my_articles";
      const { data } = await getArticles({
        author: this.$router.currentRoute.params.username,
      });

      this.articleList = data.articles;
    },
    async onFavorite(article) {
      if (article.favorited) {
        // 取消点赞
        await deleteFavorite(article.slug);
        article.favorited = false;
        article.favoritesCount += -1;
      } else {
        // 添加点赞
        await addFavorite(article.slug);
        article.favorited = true;
        article.favoritesCount += 1;
      }
    },
  },
};
</script>

<style>
</style>
