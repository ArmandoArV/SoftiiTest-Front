<script setup lang="ts">
import "./App.css";
import NumPad from "./components/NumPad/NumPad.vue";
import PaymentMethod from "./components/PaymentMethod/PaymentMethod.vue";
import { ref, computed } from "vue";

import cardImage from "./assets/card.png";
import paypalIcon from "./assets/paypal.png";
import cashIcon from "./assets/cash.png";

interface IPay {
  idPaymentMethod: number;
  methodName: string;
  paymentIcon: string;
}

// Define the base URL as a constant
const BASE_URL = "http://localhost:3001";

// Reactive state to hold the input values from NumPad
const valueFromMoneyNumpad = ref<string>("");
const numberOfPeople = ref<string>("");

// State to hold payment methods
const paymentMethods = ref<IPay[]>([]);
const selectedPaymentMethod = ref<number | null>(null); // Track selected payment method

// Fetching payment methods from the API
const fetchPaymentMethods = async () => {
  try {
    const response = await fetch(`${BASE_URL}/payment-methods`);
    const data: IPay[] = await response.json();

    // Map fetched data to include image paths
    paymentMethods.value = data.map((method) => {
      // Use a switch or if-else to assign the correct image based on the method ID
      let paymentIcon = "";

      switch (method.idPaymentMethod) {
        case 1:
          paymentIcon = cardImage;
          break;
        case 2:
          paymentIcon = paypalIcon;
          break;
        case 3:
          paymentIcon = cashIcon;
          break;
        default:
          paymentIcon = "path/to/default-icon.png"; // Fallback to a default icon if no mapping found
      }

      return {
        ...method,
        paymentIcon, // Assign the correct icon
      };
    });

    console.log("Payment methods fetched:", paymentMethods.value);
  } catch (error) {
    console.error("Error fetching payment methods:", error);
  }
};

// Call the fetch function to get payment methods
fetchPaymentMethods();

// Function to handle the emitted input value (total tip)
const handleInputValue = (value: string) => {
  valueFromMoneyNumpad.value = value; // Store the total tip value
};

// Function to handle the emitted people count value
const handlePeopleCount = (value: string) => {
  numberOfPeople.value = value; // Store the number of people
};

// Computed property to calculate the final count per person
const finalCount = computed(() => {
  const totalTip = parseFloat(valueFromMoneyNumpad.value);
  const peopleCount = parseInt(numberOfPeople.value, 10);

  if (peopleCount > 0) {
    const count = totalTip / peopleCount;
    return `$${count.toFixed(2)}`;
  } else {
    return "$0.00";
  }
});

// Function to select a payment method
const selectPaymentMethod = (id: number) => {
  selectedPaymentMethod.value = id; // Set to the clicked method's ID
};
</script>

<template>
  <main>
    <div class="mainTop">
      <div class="left">
        <div class="leftTop">
          <div class="tipContainer">
            <p class="tipTitle">Total de propinas</p>
            <div class="tipValueContainer">
              <span class="tipValue">{{ valueFromMoneyNumpad }}</span>
            </div>
          </div>
        </div>
        <div class="leftMedium">
          <p class="peopleTitle">
            ¿Entre cúantos quieres dividir las Propinas?
          </p>
          <div class="peopleAmountContainer">
            <div class="amountContainer">{{ numberOfPeople }}</div>
            <div class="finalCountContainer">
              <span class="finalCount">{{ finalCount }}</span>
              <span class="perPerson">x persona</span>
            </div>
          </div>
        </div>
        <div class="leftBottom">
          <div class="paymentTitle">
            <div class="cardImgContainer">
              <img src="./assets/cardsIcon.png" alt="" class="cardImg" />
            </div>
            <div class="paymentText">Elige el Método de Pago</div>
          </div>
          <div class="paymentMethodsContainer">
            <!-- Render payment methods -->
            <div v-for="method in paymentMethods" :key="method.idPaymentMethod">
              <PaymentMethod
                :image="method.paymentIcon"
                :text="method.methodName"
                :selected="selectedPaymentMethod === method.idPaymentMethod"
                @select="selectPaymentMethod(method.idPaymentMethod)"
              />
            </div>
          </div>
        </div>
      </div>
      <div class="middle">
        <NumPad
          @inputValue="handleInputValue"
          @peopleCount="handlePeopleCount"
        />
      </div>
      <div class="right">aaa</div>
    </div>
    <div class="mainBottom"></div>
  </main>
</template>
