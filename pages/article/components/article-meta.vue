<template>
  <div class="article-meta">
    <nuxt-link
      :to="{
        name: 'profile',
        params: {
          username: article.author.username,
        },
      }"
    >
      <img :src="article.author.image" />
    </nuxt-link>
    <div class="info">
      <nuxt-link
        class="author"
        :to="{
          name: 'profile',
          params: {
            username: article.author.username,
          },
        }"
      >
        {{ article.author.username }}
      </nuxt-link>
      <span class="date">{{ article.createdAt | date("MMM DD, YYYY") }}</span>
    </div>
    <button
      class="btn btn-sm btn-outline-secondary"
      :class="{
        active: article.author.following,
      }"
      v-if="user.username != article.author.username"
    >
      <i class="ion-plus-round"></i>
      &nbsp; Follow {{ article.author.username }}
    </button>
    <nuxt-link
      class="btn btn-sm btn-outline-secondary"
      v-else
      :to="{
        name: 'editor',
        params: { slug: article.slug },
      }"
    >
      <i class="ion-edit"></i>
      &nbsp; 编辑文章
    </nuxt-link>

    &nbsp;&nbsp;
    <button
      class="btn btn-sm btn-outline-primary"
      :class="{
        active: article.favorited,
      }"
      v-if="user.username != article.author.username"
    >
      <i class="ion-heart"></i>
      &nbsp; Favorite Post
      <span class="counter">({{ article.favoritesCount }})</span>
    </button>
    <button
      class="btn btn-sm btn-outline-danger"
      v-else
      @click.prevent="delArticle"
    >
      <i class="ion-trash-a"></i>
      &nbsp; 删除文章
    </button>
  </div>
</template>

<script>
import { mapState } from "vuex";
import { delArticle } from "@/api/article";
export default {
  name: "ArticleMeta",
  props: {
    article: {
      type: Object,
      required: true,
    },
  },
  computed: {
    ...mapState(["user"]),
  },
  methods: {
    async delArticle() {
      await delArticle(this.article.slug);
      this.$router.go(-1);
    },
  },
};
</script>

<style>
</style>
