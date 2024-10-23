<script setup lang="ts">
import { defineProps, withDefaults } from "vue";
import "./style.css";

const props = withDefaults(
  defineProps<{
    image: string;
    paymentMethod: number;
    amount: string;
  }>(),
  {
    image: "", // Default value for image
    paymentMethod: 0, // Default value for payment method
    amount: "0.00", // Default value for amount
  }
);
</script>

<template>
  <div class="paymentContainer">
    <div class="leftContainer">
      <img :src="image" alt="Payment Method Image" class="image" />
      <p class="text">{{ paymentMethod }}</p>
    </div>
    <div class="rightContainer">
      <p class="text">{{ amount }}</p>
    </div>
  </div>
</template>
