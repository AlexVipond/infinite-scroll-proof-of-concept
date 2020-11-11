<template>
  <InfiniteScroll
    :onScrollToEnd="fetchMoreItems"
    :isLoading="isLoading"
  >
    <ul
      class="flex flex-col items-center gap-4"
    >
      <li
        v-for="{ id, name, species, house } in items"
        :key="id"
        class="w-full max-w-4 rounded-4 overflow-hidden shadow-5 bg-white"
      >
        <article class="flex gap-4">
          <section
            class="min-h-full w-8 flex-none"
            :class="[
              (() => {
                switch (house) {
                  case 'Gryffindor':
                    return 'bg-red-90'
                  case 'Ravenclaw':
                    return 'bg-blue-70'
                  case 'Hufflepuff':
                    return 'bg-yellow-50'
                  case 'Slytherin':
                    return 'bg-green-70'
                  default:
                    return 'bg-gray-80'
                }
              })()
            ]"
          />
          <section class="flex flex-col gap-3 p-4">
            <span class="text-6 font-6">{{ name }}</span>
            <span class="uppercase tracking-3 font-6 text-3 text-gray-60">{{ species }}</span>
            <span class="uppercase tracking-3 font-6 text-3 text-gray-60">{{ house || 'House unknown' }}</span>
          </section>
        </article>
      </li>
    </ul>    
  </InfiniteScroll>
</template>

<script>
import { ref, computed } from 'vue'
import { useDelayable } from '@baleada/vue-composition'
import allItems from './allItems' // Normally this data would be fetched on the fly
import InfiniteScroll from './InfiniteScroll.vue'

export default {
  components: {
    InfiniteScroll,
  },
  setup () {
    const fetchMoreItems = () => delayable.value.delay(),
          delayable = useDelayable(
            () => (items.value = [...items.value, ...allItems.slice(items.value.length, items.value.length + 10)]),
            { delay: 2000 }, // Fake delay to simulate data fetching
          ),
          items = ref(allItems.slice(0, 10)),
          isLoading = computed(() => delayable.value.status === 'delaying')

    return {
      fetchMoreItems,
      items,
      isLoading,
    }
  }
}
</script>
