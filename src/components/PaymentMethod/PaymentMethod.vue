<script setup lang="ts">
import { defineProps } from 'vue';
import './style.css';

const props = defineProps<{
  image: string;
  text: string;
  selected: boolean; // Accept selected prop
}>();
</script>

<template>
  <div
    class="paymentContainer"
    :class="{ selected: selected }"
    @click="$emit('select')"
  >
    <img :src="image" alt="Image" class="image" />
    <p class="text">{{ text }}</p>
  </div>
</template>