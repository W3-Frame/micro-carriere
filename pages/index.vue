<template>
  <div>
    Home Page
    {{ isAuthenticated }}
    <!-- Logout button -->
    <UButton label="Sign Out" @click="logout" />
  </div>
</template>

<script setup lang="ts">
import { signOut } from "firebase/auth";
const { auth, db } = useFirebase();
const { isAuthenticated, user } = useAuth(auth);
watchEffect(() => {
  console.log(isAuthenticated.value);

  // redirect if not authenticated
  if (!isAuthenticated.value) {
    navigateTo("/login");
  }
});
const logout = () => {
  signOut(auth);
};
</script>

<style scoped></style>
