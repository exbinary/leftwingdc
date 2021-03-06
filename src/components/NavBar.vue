<template>
  <header>
    <nav class="flex flex-wrap items-center justify-between text-4xl text-red-700 p-5">
      <a href="/" @click.prevent="toggle" class="flex-grow">
        {{ breadcrumbs[0] == 'Home' ? 'Left Wing DC' : breadcrumbs[0] }}

        <span v-if="breadcrumbs[1]" class="text-3xl">
          &gt;
          {{ breadcrumbs[1] }}
        </span>
      </a>

      <div class="block">
        <button
          @click="toggle"
          class="px-3 py-2 border rounded border-red-700 text-red-700 focus:outline-none"
        >
          <svg class="fill-current h-4 w-4" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg">
            <title>Menu</title>
            <path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z"/>
          </svg>
        </button>
      </div>

      <transition name="fade">
        <div
          v-show="!collapsed"
          class="block w-full text-black"
        >
          <g-link
            v-for="item in remainingNavPages"
            :key="item.path"
            :to="item.path"
            class="block w-full hover:text-red-700"
          >
            {{ item.title }}
          </g-link>
        </div>
      </transition>

      <div class="block w-full">
        <hr class="border-red-700 shadow mt-4" />
      </div>
    </nav>
  </header>
</template>

<static-query>
query {
  navPages: allContentPage(filter: {navPage: {eq: true}}, sortBy: "navIndex", order: ASC) {
    edges {
      node {
        title
        path
      }
    }
  },
}
</static-query>

<script>
export default {
  data() {
    return {
      collapsed: true,
    }
  },
  homePage: {
    title: 'Home',
    path: '/',
  },
  computed: {
    currentPath() {
      return this.$route.path
    },
    currentPage() {
      return this.$page.currentPage || this.$options.homePage
    },
    breadcrumbs() {
      return [this.currentPage.parent, this.currentPage.title].filter(crumb => crumb)
    },
    allNavPages() {
      return this.$static.navPages.edges.map((edge) => edge.node).concat([this.$options.homePage])
    },
    remainingNavPages() {
      return this.allNavPages.filter(page => page.title != this.breadcrumbs[0])
    },
  },
  watch: {
    currentPath() {
      this.collapsed = true
    },
  },
  methods: {
    toggle() {
      this.collapsed = !this.collapsed
    },
  },
}
</script>

<style>
.fade-enter-active, .fade-leave-active { transition: all .5s ease-in; }
.fade-enter, .fade-leave-to { opacity: 0; }
</style>
