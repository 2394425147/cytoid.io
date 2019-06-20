<template lang="pug">
  div
    a-card.ele3
      p.heading Description
      markdown-editor(
        v-model="description"
        ref="markdownEditor"
        :configs="{ spellChecker: false }"
        style="margin-top: 4px;")
      p.heading Tags
      vue-tags-input(
        placeholder="Add tags..."
        v-model="tag"
        :tags="tags"
        @tags-changed="newTags => this.tags = newTags"
        style="max-width: unset; color: hsla(226, 68%, 6%, 1); font-size: 16px; border-radius: 2px;"
      )
      p.heading(style="margin-top: 24px;") Visibility
      a-select(v-model="visibility" style="margin-right: 8px;")
        a-select-option(value="public")
          font-awesome-icon(icon="globe" style="margin-right: 4px;" fixed-width)
          span Public
        a-select-option(value="unlisted")
          font-awesome-icon(icon="eye-slash" style="margin-right: 4px;" fixed-width)
          span Unlisted
        a-select-option(value="private")
          font-awesome-icon(icon="lock" style="margin-right: 4px;" fixed-width)
          span Private
      span(v-if="visibility === 'public'") This level will be visible to everybody.
      span(v-if="visibility === 'unlisted'") This level will only be visible to anybody who has the link.
      span(v-if="visibility === 'private'") This video will be only visible to you.
      p.heading(style="margin-top: 24px;") Moderation
      a-switch(v-model="featured" style="margin-right: 8px;")
      span Featured
      a-button(class="card-button" style="width: 100%; margin-top: 24px;")
        font-awesome-icon(icon="save" fixed-width style="margin-right: 4px;")
        span Save

</template>

<script>
export default {
  name: 'LevelManageListing',
  layout: 'background',
  data() {
    return {
      description: 'Hi',
      tag: '',
      tags: [],
      visibility: 'public',
      featured: false
    }
  },
  asyncData({ $axios, params, store }) {
    return Promise.all([
      $axios.get('/levels/' + params.id),
      $axios.get(`/levels/${params.id}/ratings`)
    ])
      .then(([levelResponse, ratingResponse]) => {
        store.commit('setBackground', { source: levelResponse.data.bundle.background })
        return {
          level: levelResponse.data,
          ratings: ratingResponse.data,
        }
      })
  },
  mounted() {

  },
  methods: {
  }
}
</script>

<style lang="less" scoped>
  .ant-card {
    margin-bottom: 16px;
  }
</style>

<style lang="less">
  .ti-input {
    border: none !important;
  }
  .editor-toolbar {
    padding: 0 4px;
    border: none;
  }
  .editor-toolbar button {
    color: #fff !important;
  }
  .editor-toolbar button.active, .editor-toolbar button:hover {
    background: rgba(0, 0, 0, 0.3) !important;
    border-color: rgba(0, 0, 0, 0) !important;
  }
  .editor-toolbar.fullscreen {
    background: #343a40 !important;
  }
  .editor-toolbar.fullscreen::before, .editor-toolbar.fullscreen::after {
    background: none !important;
  }
  .editor-preview-side {
    color: #343a40 !important;
  }
  .CodeMirror {
    border: none;
    border-radius: 2px;
  }
</style>