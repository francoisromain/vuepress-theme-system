<template>
  <div class="page">
    <Sidebar
      class="sidebar"
      :items="sidebarItems"
      :class="{ hide: !sidebarVisible }"
    />

    <div class="hero mb-xl">
      <picture 
        alt="hero"
        class="hero-img"
      >
        <source
          media="(min-width: 764px)"
          :srcset="$withBase(data.image)"
        >
        <source
          media="(min-width: 352px)"
          :srcset="$withBase(data.imageTablet)"
        >
        <img
          v-if="data.image"
          :src="$withBase(data.imageMobile)"
        >
      </picture>
      <div class="overlay-color absolute full" />

      <div class="hero-content">
        <div class="container">
          <h1 class="mb-s">
            {{ data.heroText || $title || 'Hello' }}
          </h1>

          <h4 class="description">
            {{ data.tagline || $description || 'Welcome to your VuePress site' }}
          </h4>

          <p
            v-if="data.actionText && data.actionLink"
            class="action text-center"
          >
            <NavLink
              class="btn p"
              :item="actionLink"
            />
          </p>
        </div>
      </div>
    </div>

    <div
      class="container mb-l"
    >
      <div class="desktop-blobs">
        <div
          v-if="data.features && data.features.length"
          v-for="(feature, index) in data.features"
          :key="index"
          class="desktop-blob-1-3"
        >
          <router-link
            :to="feature.link"
          >
            <h2>{{ feature.title }}</h2>
            <p>{{ feature.details }}</p>
          </router-link>
        </div>
        <div
          v-if="data.specials && data.specials.length"
          v-for="(special, index) in data.specials"
          :key="index"
          class="desktop-blob-2-3"
        >
          <router-link
            :to="special.link"
            class="p border bg-neutral block"
          >
            <h2 class="flex">
              {{ special.title }}
              <i
                class="icon-32 icon-business flex-right"
              /> 
            </h2>
            <p class="mb-0">
              {{ special.details }}
            </p>
          </router-link>
        </div>
      </div>
    </div>

    <Content class="custom" />
  </div>
</template>

<script>
import Sidebar from './Sidebar.vue'
import NavLink from './NavLink.vue'

export default {
  components: { NavLink, Sidebar },
  props: ['sidebarItems', 'sidebarVisible'],

  computed: {
    data() {
      return this.$page.frontmatter
    },

    actionLink() {
      return {
        link: this.data.actionLink,
        text: this.data.actionText
      }
    }
  }
}
</script>
