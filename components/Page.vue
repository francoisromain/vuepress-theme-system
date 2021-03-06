<template>
  <div class="container">
    <div class="desktop-blobs">
      <Sidebar
        class="sidebar desktop-blob-1-3 large-blob-1-4"
        :items="sidebarItems"
        :class="{ hide: !sidebarVisible }"
      />
      <div 
        class="desktop-blob-2-3 large-blob-3-4"
      >
        <Content slot-key="header" />
        <Content />


        <div
          v-if="contentMounted"
          class="page-edit"
        >
          <div
            v-if="editLink"
            class="edit-link"
          >
            <a
              :href="editLink"
              target="_blank"
              rel="noopener noreferrer"
            >
              {{ editLinkText }}
            </a>
            <OutboundLink />
          </div>

          <div
            v-if="lastUpdated"
            class="last-updated"
          >
            <span class="prefix">
              {{ lastUpdatedText }}:
            </span>
            <span class="time">
              {{ lastUpdated }}
            </span>
          </div>
        </div>

        <div
          v-if="contentMounted && (prev || next)"
          class="page-nav"
        >
          <p class="inner">
            <span
              v-if="prev"
              class="prev"
            >
              ←
              <router-link
                v-if="prev"
                class="prev"
                :to="prev.path"
              >
                {{ prev.title || prev.path }}
              </router-link>
            </span>
        
            <span
              v-if="next"
              class="next"
            >
              <router-link
                v-if="next"
                :to="next.path"
              >
                {{ next.title || next.path }}
              </router-link>→
            </span>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Sidebar from './Sidebar.vue'
import { resolvePage, normalize, outboundRE, endingSlashRE } from '../util'

export default {
  components: { Sidebar },
  props: ['sidebarItems', 'sidebarVisible'],

  computed: {
    contentMounted() {
      return this.$vuepress.$get('contentMounted')
    },

    lastUpdated() {
      return this.$page.lastUpdated
    },

    lastUpdatedText() {
      if (typeof this.$themeLocaleConfig.lastUpdated === 'string') {
        return this.$themeLocaleConfig.lastUpdated
      }
      if (typeof this.$site.themeConfig.lastUpdated === 'string') {
        return this.$site.themeConfig.lastUpdated
      }
      return 'Last Updated'
    },

    prev() {
      const prev = this.$page.frontmatter.prev
      if (prev === false) {
      } else if (prev) {
        return resolvePage(this.$site.pages, prev, this.$route.path)
      } else {
        return resolvePrev(this.$page, this.sidebarItems)
      }
    },

    next() {
      const next = this.$page.frontmatter.next
      if (next === false) {
      } else if (next) {
        return resolvePage(this.$site.pages, next, this.$route.path)
      } else {
        return resolveNext(this.$page, this.sidebarItems)
      }
    },

    editLink() {
      if (this.$page.frontmatter.editLink === false) {
        return
      }
      const {
        repo,
        editLinks,
        docsDir = '',
        docsBranch = 'master',
        docsRepo = repo
      } = this.$site.themeConfig

      let path = normalize(this.$page.path)
      if (endingSlashRE.test(path)) {
        path += 'README.md'
      } else {
        path += '.md'
      }
      if (docsRepo && editLinks) {
        return this.createEditLink(repo, docsRepo, docsDir, docsBranch, path)
      }
    },

    editLinkText() {
      return (
        this.$themeLocaleConfig.editLinkText ||
        this.$site.themeConfig.editLinkText ||
        `Edit this page`
      )
    }
  },

  methods: {
    createEditLink(repo, docsRepo, docsDir, docsBranch, path) {
      const bitbucket = /bitbucket.org/
      if (bitbucket.test(repo)) {
        const base = outboundRE.test(docsRepo) ? docsRepo : repo
        return (
          base.replace(endingSlashRE, '') +
          `/${docsBranch}` +
          (docsDir ? '/' + docsDir.replace(endingSlashRE, '') : '') +
          path +
          `?mode=edit&spa=0&at=${docsBranch}&fileviewer=file-view-default`
        )
      }

      const base = outboundRE.test(docsRepo)
        ? docsRepo
        : `https://github.com/${docsRepo}`

      return (
        base.replace(endingSlashRE, '') +
        `/edit/${docsBranch}` +
        (docsDir ? '/' + docsDir.replace(endingSlashRE, '') : '') +
        path
      )
    }
  }
}

function resolvePrev(page, items) {
  return find(page, items, -1)
}

function resolveNext(page, items) {
  return find(page, items, 1)
}

function find(page, items, offset) {
  const res = []
  items.forEach(item => {
    if (item.type === 'group') {
      res.push(...(item.children || []))
    } else {
      res.push(item)
    }
  })
  for (let i = 0; i < res.length; i++) {
    const cur = res[i]
    if (cur.type === 'page' && cur.path === decodeURIComponent(page.path)) {
      return res[i + offset]
    }
  }
}
</script>