<script setup>
defineProps({
  items: {
    type: Array,
    default: () => [],
  },
})

const emit = defineEmits(['remove'])
</script>

<template>
  <aside class="rounded-3xl border-white/40 bg-white/40 p-5 shadow-sm backdrop-blur-md">
    <div class="mb-4 flex items-center justify-between">
      <h2 class="text-xl font-bold text-fuchsia-950">Watchlist</h2>
      <span
        class="rounded-full border border-fuchsia-100 bg-white/80 px-3 py-0.5 text-xs font-black text-fuchsia-950 shadow-sm"
        >{{ items.length }}
      </span>
    </div>

    <p
      v-if="!items.length"
      class="rounded-2xl border border-pink-200/40 bg-white/30 p-4 text-sm leading-relaxed font-semibold text-[rgb(112,26,117)]"
    >
      No picks saved yet. Spin and add an anime to build your watchlist.
    </p>

    <ul
      v-else
      class="space-y-3"
    >
      <li
        v-for="item in items"
        :key="item.mal_id"
        class="rounded-2xl border border-white/50 bg-white/50 p-3 shadow-sm transition-all duration-200 hover:bg-white/70"
      >
        <div class="flex gap-3">
          <img
            v-if="item.image"
            :src="item.image"
            :alt="item.title"
            class="h-20 w-14 rounded-md border border-fuchsia-100 object-cover shadow-sm"
            loading="lazy"
          />
          <div
            v-else
            class="flex h-20 w-14 items-center justify-center rounded-xl border border-fuchsia-100/50 bg-fuchsia-50 text-xs font-bold text-fuchsia-300"
          >
            N/A
          </div>

          <div class="min-w-0 flex-1">
            <h3 class="truncate text-sm font-black text-fuchsia-950">{{ item.title }}</h3>
            <p class="mt-1 text-xs font-bold text-fuchsia-900/80">
              ⭐ {{ item.score ?? 'N/A' }} · Ep {{ item.episodes ?? '?' }} ·{{
                item.rating || 'Unrated'
              }}
            </p>
            <div class="mt-3 flex flex-wrap gap-2">
              <a
                :href="item.url"
                target="_blank"
                rel="noopener noreferrer"
                class="text-xs font-black text-purple-700 hover:underline"
              >
                MAL Link
              </a>
              <button
                type="button"
                class="text-xs font-black text-rose-600 hover:underline"
                @click="emit('remove', item.mal_id)"
              >
                Remove
              </button>
            </div>
          </div>
        </div>
      </li>
    </ul>
  </aside>
</template>
