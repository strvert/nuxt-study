<template>
  <div>
    <NuxtContent :document="post" />
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  onMounted,
  reactive,
  computed,
  ref,
  useContext,
} from '@nuxtjs/composition-api'

import { IContentDocument } from '@nuxt/content/types/content'

interface Article {
  post: IContentDocument | null
}

export default defineComponent({
  setup(props, ctx) {
    const article = reactive<Article>({ post: null })
    const post = computed(() => article.post)
    const { $content } = useContext()

    const reloadArticle = async () => {
      article.post = <IContentDocument>await $content('hello').fetch()
    }
    onMounted(() => {
      reloadArticle()
      window.$nuxt.$on('content:update', async () => {
        reloadArticle()
      })
    })

    const layout = () => ref("default")

    return { post, layout }
  },
})
</script>
