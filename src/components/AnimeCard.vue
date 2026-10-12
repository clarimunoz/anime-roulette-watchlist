<script setup>
import { computed, ref, watch } from 'vue'

const props = defineProps({
  anime: {
    type: Object,
    default: null,
  },
  loading: {
    type: Boolean,
    default: false,
  },
  error: {
    type: String,
    default: '',
  },
  inWatchList: {
    type: Boolean,
    default: false,
  },
})

const emit = defineEmits(['add'])

const synopsisExpanded = ref(false)

const animeImage = computed(() => {
  return (
    props.anime?.images?.jpg?.large_image_url ||
    props.anime?.images?.jpg?.image_url ||
    props.anime?.images?.webp?.large_image_url ||
    props.anime?.images?.webp?.image_url ||
    ''
  )
})

const synopsis = computed(() => {
  return props.anime?.synopsis || 'No synopsis available yet.'
})

const needsTruncation = computed(() => synopsis.value.length > 240)

const visibleSynopsis = computed(() => {
  if (synopsisExpanded.value || !needsTruncation.value) {
    return synopsis.value
  }

  return `${synopsis.value.slice(0, 240)}...`
})

watch(
  () => props.anime?.mal_id,
  () => {
    synopsisExpanded.value = false
  },
)
</script>

<template>
  <section class="rounded-3xl border border-white/40 bg-white/40 p-6 shadow-sm backdrop-blur-md">
    <div
      v-if="props.loading"
      class="space-y-4"
    >
      <div class="flex items-center gap-3 text-fuchsia-900">
        <div
          class="h-5 w-5 animate-spin rounded-full border-2 border-fuchsia-700 border-t-transparent"
        ></div>
        <p class="font-semibold tracking-wide">Shuffling anime reels...</p>
      </div>

      <div class="grid grid-cols-3 gap-2">
        <div class="h-3 animate-pulse rounded bg-fuchsia-200/50"></div>
        <div class="h-3 animate-pulse rounded bg-fuchsia-200/30"></div>
        <div class="h-3 animate-pulse rounded bg-fuchsia-200/30"></div>
      </div>
      <div class="h-72 animate-pulse rounded-2xl bg-fuchsia-200/40"></div>
    </div>

    <div
      v-else-if="error"
      class="rounded-2xl border border-red-300/40 bg-red-500/10 p-4 text-red-900"
    >
      <h3 class="text-lg font-semibold">Spin failed</h3>
      <p class="mt-2 text-sm opacity-90">{{ error }}</p>
    </div>

    <div
      v-else-if="anime"
      class="space-y-4"
    >
      <div class="overflow-hidden rounded-2xl border border-white/50 bg-white/50">
        <img
          v-if="animeImage"
          :src="animeImage"
          :alt="anime.title"
          class="h-80 w-full object-contain"
          loading="lazy"
        />
        <div
          v-else
          class="flex h-80 items-center justify-center bg-fuchsia-100 text-fuchsia-400"
        >
          No poster available
        </div>
      </div>

      <div>
        <h2 class="text-2xl font-black tracking-tight text-fuchsia-950">{{ anime.title }}</h2>
        <p class="mt-1 text-xs font-bold tracking-wide text-fuchsia-900/80">
          Score: <span class="font-black text-fuchsia-950">{{ anime.score ?? 'N/A' }}</span>
          Episodes:
          <span class="font-black text-fuchsia-950">{{ anime.episodes ?? 'Unknown' }}</span>
          Rating:
          <span class="rounded-full bg-pink-100/80 px-2 py-0.5 font-black text-pink-600">{{
            anime.rating || 'G'
          }}</span>
        </p>
      </div>

      <p class="text-sm leading-relaxed font-medium text-[rgb(112,26,117)]">
        {{ visibleSynopsis }}
        <button
          v-if="needsTruncation"
          type="button"
          class="ml-2 font-black text-fuchsia-950 underline-offset-4 hover:underline"
          @click="synopsisExpanded = !synopsisExpanded"
        >
          {{ synopsisExpanded ? 'Show less' : 'Read more' }}
        </button>
      </p>

      <div class="flex flex-wrap gap-3">
        <button
          type="button"
          class="flex cursor-pointer items-center justify-center gap-2 rounded-full border border-fuchsia-300/70 bg-[linear-gradient(to_right,#e84393,#d63031_0%,#e056fd_100%)] px-6 py-3 text-base font-black tracking-wide text-white transition-all duration-200 hover:shadow-[0_0_20px_rgba(224,86,253,0.6)] hover:brightness-110 disabled:cursor-not-allowed disabled:opacity-60"
          :disabled="inWatchList"
          @click="emit('add', anime)"
        >
          <span>{{ inWatchList ? 'In Watchlist' : 'Add to Watchlist' }}</span>

          <!-- Removed xmlns completely to stop the Vue linter error -->
          <svg
            class="h-5 w-5 stroke-current text-white"
            fill="none"
            viewBox="0 0 24 24"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="3"
              d="M12 4v16m8-8H4"
            ></path>
          </svg>
        </button>

        <a
          :href="anime.url"
          target="_blank"
          rel="noopener noreferrer"
          class="flex items-center justify-center rounded-full border border-fuchsia-800/20 px-6 py-3 text-sm font-semibold text-fuchsia-800 transition-all duration-200 hover:shadow-[0_0_20px_rgba(224,86,253,0.6)] hover:brightness-110"
        >
          Open on MAL
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="32"
            height="32"
            viewBox="0 0 1024 1024"
          >
            <!-- Icon from Element Plus by Element Plus - https://github.com/element-plus/element-plus-icons/blob/main/packages/svg/package.json -->
            <path
              fill="currentColor"
              d="M384 192v640l384-320z"
            />
          </svg>
        </a>
      </div>
    </div>

    <div
      v-else
      class="rounded-2xl border border-white/50 bg-white/50 p-6 text-center text-fuchsia-900"
    >
      Pull the lever to request your first random anime.
    </div>
  </section>
</template>
