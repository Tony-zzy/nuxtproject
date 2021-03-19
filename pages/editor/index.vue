<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">
        <div class="col-md-10 offset-md-1 col-xs-12">
            <ul class="error-messages">
            <template v-for="(messages, field) in errors">
              <li v-for="(message, index) in messages" :key="index">
                {{ field }} {{ message }}
              </li>
            </template>
          </ul>
          <form   @submit.prevent="onSubmit">
        
              <fieldset class="form-group">
                <input
                  type="text"
                  v-model="article.title"
                  class="form-control form-control-lg"
                  placeholder="Article Title"
                  required
                />
              </fieldset>
              <fieldset class="form-group">
                <input
                  type="text"
                  v-model="article.description"
                  class="form-control"
                  placeholder="What's this article about?"
                  required
                />
              </fieldset>
              <fieldset class="form-group">
                <textarea
                  class="form-control"
                  v-model="article.body"
                  rows="8"
                  placeholder="Write your article (in markdown)"
                  required
                ></textarea>
              </fieldset>
              <fieldset class="form-group">
                <div class="input-group mb-3">
                  <input
                    type="text"
                    v-model="article.currentTag"
                    class="form-control"
                    placeholder="Enter tags"
                  />
                  <div class="input-group-append">
                    <button
                      class="btn btn-primary"
                      type="button"
                      @click="addTag"
                    >
                      +
                    </button>
                  </div>
                </div>

                <div class="tag-list">
                  <div
                    class="label label-primary"
                    v-for="item in article.tagList"
                    v-bind:key="item"
                  >
                    {{ item }}
                  </div>
                </div>
              </fieldset>
              <button
                class="btn btn-lg pull-xs-right btn-primary"
                type="submit"
              >
                Publish Article
              </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { createArticle } from "@/api/article";
export default {
  // 在路由匹配组件渲染之前会先执行中间件处理
  middleware: "authenticated",
  name: "EditorIndex",
  data() {
    return {
      article: {
        title: "",
        description: "",
        body: "",
        currentTag: "",
        tagList: [],
      },
       errors: {}, // 错误信息
    };
  },
  methods: {
    async onSubmit() {
      try {
        console.log(this.article)
        await createArticle({
          article: this.article,
        });
      } catch (err) {
        // console.log('请求失败', err)
        this.errors = err.response.data.errors;
      }
    },
    addTag() {
      if (this.article.currentTag) {
        this.article.tagList.push(this.article.currentTag);
        this.article.currentTag = "";
      }
    },
  },
};
</script>

<style>
</style>
