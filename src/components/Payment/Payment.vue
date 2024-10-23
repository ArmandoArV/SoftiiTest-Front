<script setup lang="ts">
import { defineProps, withDefaults, computed } from "vue";
import "./style.css";

const paymentMethodMap: { [key: number]: string } = {
  1: "Card",
  2: "Cash",
  3: "Paypal",
};

// Props definition
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

// Log the props to the console
console.log("Received props:", {
  image: props.image,
  paymentMethod: props.paymentMethod,
  amount: props.amount,
});

const paymentMethodName = computed(() => {
  console.log("Payment method in computed:", props.paymentMethod);
  return paymentMethodMap[props.paymentMethod] || "Unknown Method";
});
</script>

<template>
  <div class="paymentContainer">
    <div class="leftContainer">
      <img :src="image" alt="Payment Method Image" class="image" />
      <p class="text">{{ paymentMethodName }}</p>
    </div>
    <div class="rightContainer">
      <p class="text">{{ amount }}</p>
    </div>
  </div>
</template>
