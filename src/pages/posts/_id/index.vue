<template lang="pug">
  .section: .container(style="margin-top: 256px;")
    h1(class="text-ele" style="margin-bottom: 16px; line-height: 1.0;" v-text="post.title")
    div(class="text-ele" style="color: rgba(255, 255, 255, 0.9); font-size: 16px; margin-bottom: 20px;" v-text="post.description")
    .columns
      .column.is-one-third
        .box
          player-avatar(style="margin-bottom: 16px;" :player="owner")
          template(v-if="post.tags.length > 0")
            .card-heading Tags
            .tags
              .tag(v-for="tag in post.tags" :key="tag" v-text="tag")
          .card-heading Last updated
          .card-secondary-text(style="margin-bottom: 0px;") {{$dateFormatCalendar(post.published_date)}}, {{ $dateFromNow(post.published_date) }}
        .box
          p(class="card-heading") Cover art
          p(class="card-em-text" style="margin-bottom: 4px;") {{ post.cover_artist }}
          a(:href="makeLink(post.cover_artist_url)")
            a-button(class="card-button" style="width: fit-content; margin-top: -2px; margin-bottom: 4px; padding-left: 12px; padding-right: 14px;")
              font-awesome-icon(icon="link" fixed-width style="margin-right: 4px;")
              span Source
      .column.is-two-thirds
        .box
          div(v-html="post.content")
        div(style="margin: 12px;")
          disqus(shortname="cytoid" :identifier="'posts/' + post.slug" :url="'https://cytoid.io/posts/' + post.slug")
</template>

<script>
import Disqus from 'vue-disqus/src/vue-disqus.vue'
import PlayerAvatar from '@/components/player/PlayerAvatar'
import { Meta } from '@/utils'
export default {
  layout: 'background',
  components: {
    PlayerAvatar,
    Disqus,
  },
  data: () => ({
    post: null,
    owner: {
      uid: 'Loading...',
      avatarURL: null
    }
  }),
  head() {
    const meta = new Meta(this.post.title, this.post.content)
    meta.extend('author', this.owner.name || this.owner.uid)
    meta.extend('og:image', this.post.cover_art.data.full_url)
    return meta
  },
  async asyncData({ $axios, params, store, error }) {
    const postResponse = await $axios.get(process.env.cmsURL + `/api/items/posts?filter[slug][eq]=${params.id}&fields=*.*`)
    const postData = postResponse.data.data[0]
    if (!postData) {
      error('Post not found!')
      return
    }
    store.commit('setBackground', { source: postData.cover_art.data.full_url })
    const ownerResponse = await $axios.get('/users/' + postData.owner)
    return {
      post: postData,
      owner: ownerResponse.data
    }
  },
  methods: {
    makeLink(link) {
      if (link == null) return 'about:blank'
      return (link.indexOf('://') === -1) ? 'http://' + link : link
    }
  },
}
</script>
