<template>
  <div class="mt-2 mb-4 d-flex justify-content-between" id="global_nav">
    <div class="nav">
      <router-link to="/" class="text-decoration-none "><span :class="{'text-black': !darkMode, 'text-white': darkMode}"><i :class="{'fs-4': true, 'ms-2': true, 'bi': true, 'bi-house-fill': true}"></i> Home</span></router-link>
      <span role="button" :class="{'text-white': darkMode, 'ms-md-3': true}" @click="switchDarkMode()"><i :class="{'fs-4': true, 'ms-2': true, 'bi': true, 'bi-brightness-high-fill': !darkMode, 'bi-moon-fill': darkMode }"></i> {{ darkMode ? 'Dark Mode' : 'Light Mode' }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "global_nav",
  props: {
    pathName: {
      type: String,
      default: 'console'
    }
  },
  computed: {
    darkMode: function () {
      return this.$store.state.darkMode
    }
  },
  methods: {
    switchDarkMode: function (status = -1) {
      this.$store.commit({
        type: "updateDarkInfo",
        status: status
      })
    }
  }
}
</script>

<style scoped>

</style>
