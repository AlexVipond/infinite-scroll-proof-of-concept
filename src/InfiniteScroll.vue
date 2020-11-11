<template>
  <!-- 
    You could use props to customize container style, scroll direction, etc.,
    but I recommend reimplementing this markup in each app that needs it.

    It's minimal enough that it's easier to control directly instead of through
    an esoteric props API.
  -->
  <section
    ref="observerRoot"
    class="h-screen w-screen overflow-y-scroll bg-gray-20 p-8"
  >
    <!-- Fetched content goes in this slot -->
    <slot />
    
    <!--
      More markup that is easier to reimplement than to customize with props.
      Add code snippets to your InfiniteScroll component docs to suggest
      specific markup/loading spinners/semantics/whatever.
    -->
    <div
      ref="observerTarget"
      class="h-8 w-full"
    >
      <span
        v-if="isLoading"
      >
        Loading...
      </span>
      <span
        v-else
      >
        Loaded
      </span>
    </div>    
  </section>
</template>

<script>
import { ref, onMounted, computed } from 'vue'
import { useListenable, useDelayable } from '@baleada/vue-composition'

export default {
  props: {
    isLoading: {
      type: Boolean,
      required: true,
    },
    onScrollToEnd: {
      type: Function,
      required: true,
    }
  },
  setup (props) {
    // There's some minimal setup here that I feel is not worth extracting.
    // I'd rather read docs to help me reimplement this in separate apps,
    // creating one minimally customized InfiniteScroll component per app.
    const observerTarget = ref(null),
          observerRoot = ref(null),
          isLoading = computed(() => props.isLoading)
      
    useIntersectable(
      props.onScrollToEnd,
      { root: observerRoot, target: observerTarget },
    )

    return {
      observerTarget,
      observerRoot,
      isLoading,
    }
  }
}

// _This_ is the kind of thing I'd extract for use across various apps,
// publishing on NPM, etc.
// 
// Specifically, the way you check IntersectionObserver entries to see
// if the target intersects the root element screams "implementation detail".
//
// Usage of onMounted is also pretty implementation detail-ish.
//
// Lots of other implementation details are wrapped up here in my useListenable
// function as well, e.g. automatically disconnecting the observer
// during the onBeforeUnmounted hook to avoid memory leaks.
function useIntersectable (
  callback, // required
  { root, target } // technically optional, but strongly recommended
) {
  onMounted(() => {
    intersectable.value.listen(
      entries => {
        const { 0: { isIntersecting } } = entries

        if (isIntersecting) {
          callback()
        }
      },
      {
        target: target.value,
        observer: {
          root: root.value,
          // You could allow additional params to pass options in here if you wanted
        },
      }
    )
  })

  const intersectable = useListenable('intersect')

  return intersectable
}
</script>
