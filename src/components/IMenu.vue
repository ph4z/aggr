<template>
  <div id="imenu" class="imenu" :class="{ '-open': open }">
    <button class="menu__button btn" @click="toggleMenu">
      <img class="icon-menu" src="/navbar/menu.png" />
    </button>
    <div class="menu__actions" v-if="open" @click="onClickItem">
      <button class="menu-action btn" type="button">
	<a href="https://strat.insolence.tech/"><span class="mr4">StratMap</span></a>
        <i class="icon-enlarge"></i>
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import dialogService from '@/services/dialogService'
import { PaneType } from '@/store/panes'
import { Component, Vue } from 'vue-property-decorator'
import Slider from './framework/picker/Slider.vue'
import SettingsDialog from './settings/SettingsDialog.vue'

@Component({
  name: 'Header',
  components: {
    Slider
  }
})
export default class extends Vue {
  isFullscreen = false
  open = false
  paneTypes = {
    chart: {
      title: 'Chart',
      description: 'Live chart'
    },
    trades: {
      title: 'Trades',
      description: 'Trades feed'
    },
    stats: {
      title: 'Stats',
      description: 'Custom rolling metrics'
    },
    counters: {
      title: 'Counters',
      description: 'Buys/sells by intervals'
    },
    prices: {
      title: 'Markets',
      description: 'Price change & volume'
    },
    website: {
      title: 'Website',
      description: 'Embed website'
    }
  }

  get useAudio() {
    return this.$store.state.settings.useAudio
  }

  get audioVolume() {
    return this.$store.state.settings.audioVolume
  }

  showSettings() {
    dialogService.open(SettingsDialog)
  }

  toggleFullscreen() {
    let element = document.body

    if (event instanceof HTMLElement) {
      element = event
    }

    ;(element as any).requestFullScreen =
      (element as any).requestFullScreen ||
      (element as any).webkitRequestFullScreen ||
      (element as any).mozRequestFullScreen ||
      function() {
        return false
      }
    ;(document as any).cancelFullScreen =
      (document as any).cancelFullScreen ||
      (document as any).webkitCancelFullScreen ||
      (document as any).mozCancelFullScreen ||
      function() {
        return false
      }

    this.isFullscreen ? (document as any).cancelFullScreen() : (element as any).requestFullScreen()
  }

  toggleMenu() {
    this.open = !this.open

    if (this.open) {
      this.isFullscreen = (document as any).webkitIsFullScreen || (document as any).mozFullScreen ? true : false
    }
  }

  addPane(type: PaneType) {
    this.$store.dispatch('panes/addPane', { type })

    this.toggleMenu()
  }

  onClickItem(event) {
    if (event.target.classList.contains('dropdown__selected') || event.target.parentElement.classList.contains('dropdown__selected')) {
      return
    }

    this.toggleMenu()
  }

  toggleAudio() {
    this.$store.commit('settings/TOGGLE_AUDIO', !this.useAudio)
  }
}
</script>

<style lang="scss">
.imenu {
  position: fixed;
  top: 4.5rem;
  right: 1.5rem;
  z-index: 10;

  .menu__button {
    width: 2.5rem;
    height: 2.5rem;
    border-radius: 50%;
    justify-content: center;
    background-color: var(--theme-background-100);
    z-index: 1;
    position: relative;

    &:hover {
      color: white;
    }
  }

  .menu__actions {
    position: absolute;
    top: 100%;
    right: 0;
    display: flex;
    flex-direction: column-reverse;
    align-items: flex-end;
    width: 0;
    filter: drop-shadow(-1em -1em 3em var(--theme-background-base));
  }

  .menu-action {
    margin-bottom: 0.625rem;
    text-decoration: none;
    white-space: nowrap;

    &.btn {
      background-color: var(--theme-background-100);

      &:hover {
        background-color: $green;
        color: white;
      }
    }
  }

  &.-open {
    .menu__button {
      background-color: $green;
      color: white;
    }
  }

  .dropdown__options {
    right: 100%;
    left: auto;
    top: auto;
    bottom: 0;
    margin-right: 0.5rem;
  }
}
</style>
