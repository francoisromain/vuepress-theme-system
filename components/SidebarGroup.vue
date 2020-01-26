<template>
  <div
    class="sidebar-group"
    :class="{ first, collapsable }"
  >
    <p
      class="sidebar-heading"
      :class="{ open }"
      @click="$emit('toggle')"
    >
      <span>{{ item.title }}</span>
      <span
        v-if="collapsable"
        class="arrow"
        :class="open ? 'down' : 'right'"
      />
    </p>

    <DropdownTransition>
      <ul
        v-if="open || !collapsable"
        ref="items"
        class="sidebar-group-items list-sans"
      >
        <li v-for="child in item.children">
          <SidebarLink :item="child" />
        </li>
      </ul>
    </DropdownTransition>
  </div>
</template>

<script>
import SidebarLink from './SidebarLink.vue'
import DropdownTransition from './DropdownTransition.vue'

export default {
  name: 'SidebarGroup',
  components: { SidebarLink, DropdownTransition },
  props: ['item', 'first', 'open', 'collapsable']
}
</script>
