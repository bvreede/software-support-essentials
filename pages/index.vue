<template>
  <div class="flex flex-col w-screen h-screen bg-gray-200 overflow-auto">
    <Banner />

    <div class="flex flex-col m-2 gap-2">
      <!-- search -->
      <input v-model="query" type="search" class="w-1/3 m-4 p-2 self-center" placeholder="search"></input>

      <!-- categories -->
      <div v-for="category in categories" :key="category" class="flex flex-wrap gap-4 mb-8">
        <h3 class="prose-xl w-full">
          {{ category }}
        </h3>
        <!-- stories -->
        <StoryCard
          v-for="story in stories.filter(story => story.category === category)"
          :key="story.id"
          :title="story.title"
          :author="story.author"
          :thumbnail="`stories/_${story.slug}/${story.thumbnail}`"
          :url="story.slug"
        />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData (context) {
    const stories = await context.$content()
      .only(['id', 'slug', 'title', 'author', 'thumbnail', 'category'])
      .fetch()
      .catch(e => console.log(e))

    const categories = stories
      .map(story => story.category)
      .filter((v, i, a) => a.indexOf(v) === i)
      .sort((a, b) => {
        return context.$config.categoryOrder.indexOf(a) - context.$config.categoryOrder.indexOf(b)
      })

    return { stories, categories }
  },
  data () {
    return {
      query: ''
    }
  },
  watch: {
    async query (query) {
      this.stories = await this.$content()
        .only(['id', 'slug', 'title', 'author', 'thumbnail', 'category'])
        .search(query)
        .fetch()
        .catch(e => console.log(e))
    }
  }
}
</script>
