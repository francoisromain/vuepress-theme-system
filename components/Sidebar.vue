<template>
  <div>
    <div class="sidebar-content">
      <ul
        v-if="items.length"
        class="sidebar-links list-sans"
      >
        <li
          v-for="(item, i) in items"
          :key="i"
        >
          <SidebarGroup
            v-if="item.type === 'group'"
            :item="item"
            :first="i === 0"
            :open="i === groupIndex"
            :collapsable="item.collapsable || item.collapsible"
            @toggle="toggleGroup(i)"
          />
          <SidebarLink
            v-else
            :item="item"
          />
        </li>
      </ul>
      <NavLinks class="desktop-hide" />
    </div>
  </div>
</template>

<script>
import SidebarGroup from './SidebarGroup.vue'
import SidebarLink from './SidebarLink.vue'
import NavLinks from './NavLinks.vue'
import { isActive } from '../util'

export default {
  components: { SidebarGroup, SidebarLink, NavLinks },

  props: ['items'],

  data() {
    return {
      groupIndex: 0
    }
  },

  watch: {
    $route() {
      this.refreshIndex()
    }
  },

  created() {
    this.refreshIndex()
  },

  methods: {
    refreshIndex() {
      const index = groupIndexResolve(this.$route, this.items)
      if (index > -1) {
        this.groupIndex = index
      }
    },

    toggleGroup(index) {
      this.groupIndex = index === this.groupIndex ? -1 : index
    }
  }
}

function groupIndexResolve(route, items) {
  for (let i = 0; i < items.length; i++) {
    const item = items[i]
    if (
      item.type === 'group' &&
      item.children.some(c => isActive(route, c.path))
    ) {
      return i
    }
  }
  return -1
}
</script>
