<template>
  <div
    class="header-wrapper"
    :class="isOptionsVisible && 'header-wrapper--expanded'"
  >
    <HeaderBanner closeable />

    <va-navbar
      v-show="!isOptionsVisible"
      class="header"
      color="background-secondary"
    >
      <template #left>
        <HeaderSelector
          class="mr-3"
          :minimized="isSidebarVisible"
          @toggle-sidebar="toggleSidebar"
        />
        <NuxtLink
          :to="landing.to"
          :aria-label="landing.text"
        >
          <VuesticLogo
            height="32"
            width="160"
            aria-hidden="true"
          />
        </NuxtLink>
        <AlgoliaSearch class="header__searchbar" />
      </template>

      <template #right>
        <div
          v-if="breakpoint.mdUp"
          class="header__options"
        >
          <va-button
            v-for="(link, index) in links"
            :key="index"
            preset="secondary"
            class="mr-1"
            :to="link.to"
            :href="link.url"
            :target="link.target"
          >
            {{ link.text }}
          </va-button>
          <ColorDropdown class="mr-2" />
          <LanguageDropdown
            class="mr-3"
            preset="secondary"
          />
          <ThemeSwitch class="mr-4" />
          <VersionDropdown />
        </div>

        <!-- options mobile menu -->
        <va-button
          v-if="breakpoint.smDown"
          :aria-label="$t('menu.openOptionsMenu')"
          preset="plain"
          @click="toggleOptions"
        >
          <va-icon class="fas fa-bars" />
        </va-button>
      </template>
    </va-navbar>

    <!-- mobile options -->
    <nav
      v-show="isOptionsVisible"
      class="header__mobile-options"
    >
      <div class="mobile-options__menu-button-wrapper">
        <va-button
          v-if="isOptionsVisible"
          :aria-label="$t('menu.closeOptionsMenu')"
          preset="plain"
          @click="toggleOptions"
        >
          <va-icon class="fas fa-times" />
        </va-button>
      </div>

      <va-list class="mobile-options__list">
        <va-list-item
          v-for="(link, index) in links"
          :key="index"
        >
          <va-list-item-section class="mobile-options__link">
            <va-button
              preset="plain"
              :to="link.to"
              :href="link.url"
              :target="link.target"
              class="mobile-options__link-button"
            >
              {{ link.text }}
            </va-button>
          </va-list-item-section>
        </va-list-item>

        <div class="mobile-options__languages">
          <va-list-label
            color="secondary"
            class="mobile-options__label"
          >
            {{ $t('landing.header.buttons.language') }}
          </va-list-label>

          <va-list-item
            v-for="({ code, name }, id) in locales"
            :key="id"
            class="mobile-options__language"
            :class="{ active: code === locale }"
            @click="setLocale(code)"
          >
            <va-list-item-section class="mobile-options__link">
              <span class="language">{{ name }}</span>
            </va-list-item-section>
          </va-list-item>

          <va-list-item>
            <va-list-item-section class="mobile-options__link">
              <router-link :to="`/${locale}/contribution/translation`">
                {{ $t('landing.header.buttons.translation') }}
              </router-link>
            </va-list-item-section>
          </va-list-item>
        </div>

        <div class="mobile-options__items">
          <ThemeSwitch class="mb-8" />
          <SocialsLinks size="large" />
          <StarsButton repo="epicmaxco/vuestic-ui" />
          <VersionDropdown />
        </div>
      </va-list>
    </nav>
  </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue'
import { useBreakpoint } from 'vuestic-ui'

import LanguageDropdown from './LanguageDropdown.vue'
import VersionDropdown from './header/VersionDropdown.vue'
import ColorDropdown from './header/ColorDropdown.vue'
import HeaderSelector from './header/HeaderSelector.vue'
import VuesticLogo from './header/VuesticDocsLogo.vue'
import AlgoliaSearch from '../AlgoliaSearch.vue'
import ThemeSwitch from './header/ThemeSwitch.vue'
import SocialsLinks from '../landing/SocialsLinks.vue'
import StarsButton from '../landing/StarsButton.vue'
import { useSharedLanguageSwitcher } from '@/composables/useLanguageSwitcher'

const { t, locale, locales, setLocale } = useSharedLanguageSwitcher()
const breakpoint = useBreakpoint()

const landing = computed(() => ({
  text: t('menu.home'),
  to: `/${locale.value}`,
}))

const links = computed(() => [
  {
    text: t('menu.github'),
    url: 'https://github.com/epicmaxco/vuestic-ui',
    target: '_blank',
  },
  {
    text: t('menu.contribution'),
    to: `/${locale.value}/contribution/guide`,
  },
])

const props = defineProps({
  isSidebarVisible: {
    type: Boolean,
    default: false,
  },
  isOptionsVisible: {
    type: Boolean,
    default: false,
  },
})

const emit = defineEmits(['update:isSidebarVisible', 'update:isOptionsVisible'])

const toggleSidebar = () => {
  emit('update:isSidebarVisible', !props.isSidebarVisible)
}

const toggleOptions = () => {
  emit('update:isOptionsVisible', !props.isOptionsVisible)
}
</script>

<style lang="scss">
@import "vuestic-ui/src/styles/resources";
@import "@/assets/smart-grid.scss";

.header-wrapper {
  --va-navbar-mobile-height: auto;
  --navbar-padding: 1.2rem 1rem;
  --navbar-padding-xs: 0.75rem 1rem;

  &--expanded {
    width: 100%;
    height: 100vh;
    position: fixed;
    left: 0;
    top: 0;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    overflow: hidden;
  }
}

.header {
  box-shadow: 0 2px 8px var(--va-shadow);

  &.va-navbar {
    padding: var(--navbar-padding);

    @include sm(display, flex);
    @include sm(flex-direction, row);
    @include sm(justify-content, space-between);
    @include sm(align-items, center);
    @include sm(height, unset);

    @include xs(padding, var(--navbar-padding-xs));
  }

  .va-navbar__left {
    align-items: center;
    flex-grow: 1;
  }

  .va-navbar__center {
    @include sm(width, unset);
  }

  .va-navbar__right {
    align-items: center;

    @include sm(width, unset);
  }

  &__searchbar {
    margin-left: 1.5rem;

    @include sm(margin-left, 1rem);
    @include xs(margin-left, auto);
    @include xs(margin-right, 1rem);
  }

  &__options {
    display: flex;
    align-items: center;
  }

  &__mobile-options {
    width: 100%;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    overflow: auto;
  }
}

.mobile-options {
  &__menu-button-wrapper {
    width: 100%;
    flex-shrink: 0;
    text-align: right;
    padding: var(--navbar-padding);

    @include xs(padding, var(--navbar-padding-xs));
  }

  &__list {
    display: flex;
    flex-direction: column;
    flex-grow: 1;
    justify-content: center;
    padding: 0 2rem 2rem;
    max-width: 25rem;
    align-self: center;
  }

  &__languages {
    margin-top: 3.5rem;
  }

  &__label {
    font-size: 0.625rem;
    margin-bottom: 0.5rem;
  }

  &__language {
    cursor: pointer;

    &.active {
      .language {
        color: var(--va-text-primary);
      }
    }
  }

  &__link {
    @include sm(font-size, 1.2rem);

    align-self: center;
    font-weight: 600;
    text-align: center;

    span,
    a {
      color: var(--va-primary);
      padding: 0.5rem 0;
      display: flex;
      align-items: center;
      justify-content: center;

      &:hover {
        color: var(--va-text-primary);
      }
    }
  }

  &__link-button {
    margin-top: 0.5rem;

    .va-button__content {
      @include sm(font-size, 1.2rem);
    }
  }

  &__items {
    margin-top: 2rem;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;

    & > * {
      margin-top: 2rem;
    }
  }
}
</style>
