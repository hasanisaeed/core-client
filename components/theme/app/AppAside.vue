<template>
  <aside class="fixed z-50 lg:z-0 lg:static">
    <div class="h-full overflow-auto pointer-events-none lg:overflow-visible">
      <!-- scrim -->
      <Transition name="fade">
        <div
          v-if="$menu.visible.value"
          class="fixed top-0 left-0 z-0 w-full h-full pointer-events-auto d-blur-header lg:hidden"
          :class="isHome ? 'd-bg-header-home' : 'd-bg-header'"
          @click.stop="$menu.toggle"
        />
      </Transition>

      <!-- Desktop aside -->
      <div
        class="hidden lg:block fixed top-0 left-0 overflow-auto pointer-events-auto min-h-fill-available h-screen sticky top-header w-60"
      >
        <div class="w-auto h-full overflow-auto">
          <AsideNavigation />
        </div>
      </div>

      <!-- Mobile aside -->
      <Transition name="slide-from-left-to-left">
        <div
          v-show="$menu.visible.value"
          class="lg:hidden fixed top-0 left-0 w-auto h-full overflow-auto pointer-events-auto min-h-fill-available border-r !w-base"
          :class="isHome ? 'border-sky-darker' : 'd-border'"
        >
          <div class="w-auto h-full overflow-auto" :class="isHome ? 'd-bg-header-home' : 'd-bg-header'">
            <div
              class="flex items-center justify-between w-full px-0.5 sm:px-2 h-header d-aside-header-bg"
              :class="isHome ? 'd-aside-header-home-bg' : 'd-aside-header-bg'"
            >
              <button
                class="transition-colors duration-200 focus:outline-none"
                :class="isHome ? 'text-gray-300 hover:text-white' : 'd-secondary-text hover:d-secondary-text-hover'"
                aria-label="backButton"
                @click.stop="mobileBack"
              >
                <IconArrowLeft v-if="!mobileMainNav && layout.aside" class="p-3 w-12 h-12" />
                <IconClose v-else class="p-3 w-12 h-12" />
              </button>
              <div class="flex items-center h-header space-x-2">
                <LangSwitcher
                  :class="{ 'text-white': isHome }"
                  :icon-class="`w-6 h-6 m-auto ${
                    isHome ? 'text-gray-300 hover:text-primary-400' : 'd-secondary-text hover:d-secondary-text-hover'
                  }`"
                  strategy="fixed"
                />
                <ColorSwitcher v-if="!isHome" />
              </div>
            </div>

            <AsideNavigation v-if="!mobileMainNav && layout.aside" />
            <AsideHeaderNavigation v-else :links="links" />
          </div>
        </div>
      </Transition>
    </div>
  </aside>
</template>

<script lang="ts">
import { defineComponent, useContext, ref, watch } from '@nuxtjs/composition-api'
import { useNav } from '~/plugins/nav'

export default defineComponent({
  props: {
    links: {
      type: Array,
      default: () => []
    }
  },
  setup() {
    const { $docus, $menu } = useContext()
    const { isHome } = useNav()

    const mobileMainNav = ref(!$docus.layout.value.aside)

    watch($menu.visible, (value, old) => {
      if (value && !old && $docus.layout.value.aside) {
        mobileMainNav.value = false
      }
    })

    function mobileBack() {
      if (!mobileMainNav.value) {
        mobileMainNav.value = true
      } else {
        $menu.close()
      }
    }

    return {
      mobileMainNav,
      mobileBack,
      layout: $docus.layout,
      isHome
    }
  }
})
</script>
