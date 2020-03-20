<template>
  <v-app dark>
    <v-container v-if="error.statusCode === 404">
      <v-row justify="center">
        <v-img src="404.svg" alt="404 illustration" max-width="60%"></v-img>
      </v-row>
      <h1 class="text-center grey--text mt-5"> {{ pageNotFound }} </h1>
    </v-container>
    <h1 v-else>
      {{ otherError }}
    </h1>
    <NuxtLink to="/" class="text-center">
      Return to Shotify
    </NuxtLink>
  </v-app>
</template>

<script>
export default {
  layout: 'empty',
  props: {
    error: {
      type: Object,
      default: null
    }
  },
  data () {
    return {
      pageNotFound: 'Page Not Found',
      otherError: 'An error occurred'
    }
  },
  head () {
    const title =
      this.error.statusCode === 404 ? this.pageNotFound : this.otherError
    return {
      title
    }
  }
}
</script>

<style scoped>
h1 {
  font-size: 20px;
}
</style>
