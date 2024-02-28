<script>
import { RouterLink, RouterView } from 'vue-router'
export default {
  components: {
    RouterLink,
    RouterView
  }
}
</script>

<template>
  <div>
    <RouterLink to="/"></RouterLink>
    <RouterView />
  </div>
</template>
