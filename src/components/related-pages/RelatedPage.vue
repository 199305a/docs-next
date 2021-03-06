<template>
  <v-sheet
    :color="dark ? undefined : 'grey lighten-5'"
    class="overflow-hidden"
    rounded
  >
    <v-list-item
      v-if="item"
      :to="url"
    >
      <v-list-item-icon>
        <v-icon
          :color="colors[section]"
          v-text="icon"
        />
      </v-list-item-icon>

      <v-list-item-content>
        <v-list-item-title v-text="item.title" />

        <v-list-item-subtitle
          class="text-capitalize"
          v-text="section"
        />
      </v-list-item-content>
    </v-list-item>
  </v-sheet>
</template>

<script>
  // Utilities
  import { get } from 'vuex-pathify'

  export default {
    name: 'RelatedPage',

    props: { to: String },

    data: () => ({
      colors: {
        'getting-started': 'teal',
        api: 'orange',
        company: 'primary',
        components: 'indigo darken-1',
        customization: 'red',
        directives: 'blue-grey',
        introduction: 'primary',
        'professional-support': 'amber darken-2',
        styles: 'deep-purple accent-4',
        themes: 'pink',
      },
      icon: undefined,
      item: undefined,
    }),

    computed: {
      ...get('pages', [
        'pages',
        'frontmatter@related',
      ]),
      dark: get('user/dark'),
      locale: get('route/params@locale'),
      nav: get('app/nav'),
      section () {
        return this.to.split('/')[1]
      },
      url () {
        return `/${this.locale}${this.to}`
      },
    },

    watch: {
      nav: {
        immediate: true,
        handler (val) {
          this.findRelatedPage(val)
        },
      },
    },

    methods: {
      findRelatedPage (items) {
        for (const item of items) {
          // If item is found, stop iterating
          if (this.item) break

          // Set icon if available
          if (item.icon) this.icon = item.icon

          // Check children
          if (item.items) {
            this.findRelatedPage(item.items)

            continue
          }

          if (item.to === this.url) {
            this.item = item
          }
        }
      },
    },
  }
</script>
