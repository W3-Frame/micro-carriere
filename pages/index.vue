<template>
  <div>
    Home Page
    {{ isAuthenticated }}
  </div>
</template>

<script setup lang="ts">

const { auth, db } = useFirebase();
const { isAuthenticated, user } = useAuth(auth);
</script>

<style scoped>

</style>