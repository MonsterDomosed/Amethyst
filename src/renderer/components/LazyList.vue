<script setup lang="ts">
import { usePlayer, useShortcuts, useState } from "@/amethyst";
import { Track } from "@/logic/track";
import BaseChip from "./new/BaseChip.vue";
import BroomIcon from "@/icons/plumpy/BroomIcon.vue";
import PlayIcon from "@/icons/plumpy/PlayIcon.vue";
import ExternalLinkIcon from "@/icons/plumpy/ExternalLinkIcon.vue";
import RestartIcon from "@/icons/plumpy/RestartIcon.vue";
import LoadingIcon from "@/icons/plumpy/LoadingIcon.vue";
import ErrorIcon from "@/icons/plumpy/ErrorIcon.vue";
import Cover from "@/components/CoverArt.vue";

defineProps<{tracks: Track[], headers: string[]}>();
const state = useState();
const player = usePlayer();
const isHoldingControl = useShortcuts().isControlPressed;
const invoke = window.electron.ipcRenderer.invoke;

// Context Menu options for this component 
const handleContextMenu = (e: MouseEvent, track: Track) => {
  state.openContextMenuAt(e.x, e.y, [
    { title: "Play", icon: PlayIcon, action: () => player.play(track) },
    { title: "Open in Explorer...", icon: ExternalLinkIcon, action: () => invoke("show-item", [track.path]) },
    { title: "Render cover art", icon: RestartIcon, action: () => track.path },
    { title: "Remove from queue", icon: BroomIcon, action: () => player.queue.remove(track) },
  ]);
};

</script>

<template>
  <RecycleScroller
    class="h-full text-12px text-left relative select-none"
    :items="tracks"
    :item-size="16"
    key-field="path"
    :buffer="32"
  >
    <template #before>
      <div
        v-for="header in headers"
        :key="header"
        class="th font-bold text-primary-900 mb-2"
      >
        {{ header }}
      </div>
    </template>
    <template
      #default="{ item }"
    >
      <div
        :class="[
          isHoldingControl && 'control-hover', 
          isHoldingControl && 'cursor-external-pointer', 
          item.hasErrored && 'opacity-50 not-allowed',
          player.getCurrentTrack()?.path == item.path && 'active'
        ]"
        class="queue"
        @contextmenu="handleContextMenu($event, item)"
        @keypress.prevent
        @click="isHoldingControl ? invoke('show-item', [item.path]) : player.play(item)"
      >
        <div
          v-if="state.settings.showMiniCovers"
          class="td max-w-4"
        >
          <loading-icon
            v-if="item.isLoading"
            class="h-3 animate-spin w-3 min-h-3 min-w-3"
          />
          <error-icon
            v-else-if="item.hasErrored"
            class="h-3 w-3 min-h-3 min-w-3"
          />
    
          <cover
            v-else-if="state.settings.showMiniCovers"
            class="w-3 h-3"
            :url="(item.isLoaded ? item.getCover() : state.state.defaultCover) as string"
          />
        </div>
        <div class="td">
          {{ player.getCurrentTrack()?.path == item.path ? "⏵ " : "" }}{{ item.getFilename() }}
        </div>
        <div class="td">
          {{ item.getArtistsFormatted() }}
        </div>
        <div class="td">
          {{ item.getAlbumFormatted() }}
        </div>
        <div class="td ">
          <BaseChip
            v-if="item.getMetadata()?.format.container"
            class="text-8px"
          >
            {{ item.getMetadata()?.format.container }}
          </BaseChip>
        </div>
        <div class="td">
          {{ item.getFilesizeFormatted() }}
        </div>
        <div class="td">
          {{ item.getDurationFormatted() }}
        </div>
      </div>
    </template>
  </RecycleScroller>
</template>

<style lang="postcss">

.vue-recycle-scroller__slot {
  @apply flex;
}

.vue-recycle-scroller__slot,
.vue-recycle-scroller__item-view {
  @apply  text-left;
}
.th,
.td {
  @apply flex-1;
}

.td {
  @apply max-w-full overflow-hidden overflow-ellipsis;
}

.vue-recycle-scroller__slot .th:first-child,
.vue-recycle-scroller__item-view .td:first-child {
  @apply w-4 max-w-4;
}

.queue {
  @apply hover:text-white text-primary-900 h-4 w-full flex;

  &.active {
    @apply text-primary-800;
  }
}

</style>