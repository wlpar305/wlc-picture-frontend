<template>
  <div id="app">
    <BasicLayout />
  </div>
</template>

<script setup lang="ts">
import BasicLayout from "@/layouts/BasicLayout.vue";
// import { healthUsingGet } from '@/api/mainController'
//
// healthUsingGet().then((res) => {
//   console.log(res)
// })
import {useLoginUserStore} from "@/stores/useLoginUserStore.ts";
const loginUserStore = useLoginUserStore()
loginUserStore.fetchLoginUser()

</script>
