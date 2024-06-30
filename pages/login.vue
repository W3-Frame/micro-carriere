<script setup lang="ts">
import type { FormError, FormSubmitEvent } from "#ui/types";
import { useFirebase } from "~/composables/firebase";
import { doc, setDoc } from "firebase/firestore";
import {
  GoogleAuthProvider,
  getAdditionalUserInfo,
  signInWithPopup,
} from "firebase/auth";

definePageMeta({
  layout: "auth",
});

console.log("Login page");

useSeoMeta({
  title: "Login",
});

const { auth, db } = useFirebase();
const { isAuthenticated, user } = useAuth(auth);

watch(
  isAuthenticated,
  (isAuthenticated) => {
    if (isAuthenticated) {
      console.log("You are authenticated");
      navigateTo("/");
    }
  },
  { immediate: true }
);
const signIn = () =>
  signInWithPopup(auth, new GoogleAuthProvider()).then(async (result) => {
    const isNewUser = getAdditionalUserInfo(result)?.isNewUser;
    const { email, displayName, photoURL, uid } = result.user;

    if (isNewUser) {
      await setDoc(doc(db, "users", uid), { email, displayName, photoURL });
    }
    navigateTo("/");
  });
const fields = [
  {
    name: "email",
    type: "text",
    label: "Email",
    placeholder: "Enter your email",
  },
  {
    name: "password",
    label: "Password",
    type: "password",
    placeholder: "Enter your password",
  },
];
const state = reactive({
  email: undefined,
  password: undefined,
});

const validate = (state: any) => {
  const errors = [];
  if (!state.email)
    errors.push({ path: "email", message: "Email is required" });
  if (!state.password)
    errors.push({ path: "password", message: "Password is required" });
  return errors;
};

const providers = [
  {
    label: "Continue with GitHub",
    icon: "i-simple-icons-github",
    color: "white" as const,
    click: () => {
      console.log("Redirect to GitHub");
    },
  },
];

function onSubmit(data: any) {
  console.log("Submitted", data);
}
</script>

<template>
  <UCard class="max-w-sm w-full bg-white/75 dark:bg-white/5 backdrop-blur">
    <!-- Header -->
    <div class="text-center">
      <div class="mb-2 pointer-events-none">
        <UIcon
          name="i-heroicons-lock-closed"
          class="w-8 h-8 flex-shrink-0 text-gray-900 dark:text-white"
        />
      </div>
      <p class="text-2xl text-gray-900 dark:text-white font-bold">
        Welcome back
      </p>
      <p class="text-gray-500 dark:text-gray-400 mt-1">
        Don't have an account?
        <a href="/signup" class="text-primary font-medium">Sign up</a>.
      </p>
    </div>
    <!-- Content -->
    <div class="flex flex-col gap-y-6">
      <UForm
        :validate="validate"
        :state="state"
        class="space-y-6"
        @submit="onSubmit"
      >
        <UFormGroup label="Email" name="email">
          <UInput v-model="state.email" placeholder="Enter Your Email" />
        </UFormGroup>

        <UFormGroup label="Password" name="password" hint="Forget Password ?">
          <template #hint>
            <NuxtLink to="/" class="text-primary font-medium"
              >Forgot password?</NuxtLink
            >
          </template>
          <UInput
            v-model="state.password"
            type="password"
            placeholder="Enter Your Password"
          />
        </UFormGroup>

        <UButton
          type="submit"
          class="w-full rounded-full text-center flex justify-center gap-x-2"
        >
          <span>Continue</span>
          <UIcon name="i-heroicons-arrow-right-20-solid" class="flex-shrink-0"
        /></UButton>
      </UForm>
      <div class="flex items-center align-center text-center w-full flex-row">
        <div
          class="flex border-gray-200 dark:border-gray-800 w-full border-t border-solid"
        ></div>
        <div
          class="font-medium text-gray-700 dark:text-gray-200 flex mx-3 whitespace-nowrap"
        >
          <span class="text-sm">or</span>
        </div>
        <div
          class="flex border-gray-200 dark:border-gray-800 w-full border-t border-solid"
        ></div>
      </div>

      <div class="space-y-3">
        <UButton
          type="submit"
          color="white"
          size="lg"
          class="w-full h-8 rounded-full text-center flex justify-center gap-x-2"
          @click="signIn()"
        >
          <UIcon name="i-simple-icons-google" class="flex-shrink-0 h-5 w-5" />
          <span>Continue With Google</span></UButton
        >
      </div>
    </div>

    <!-- Footer -->
    <p class="text-sm text-gray-500 dark:text-gray-400 mt-6 text-center">
      By signing in, you agree to our
      <NuxtLink to="/" class="text-primary font-medium"
        >Terms of Service</NuxtLink
      >.
    </p>
  </UCard>
</template>
