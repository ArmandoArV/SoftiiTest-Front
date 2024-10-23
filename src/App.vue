<script setup lang="ts">
import "./App.css";
import NumPad from "./components/NumPad/NumPad.vue";
import PaymentMethod from "./components/PaymentMethod/PaymentMethod.vue";
import { ref, computed } from "vue";
import Payment from "./components/Payment/Payment.vue";

import cardImage from "./assets/card.png";
import paypalIcon from "./assets/paypal.png";
import cashIcon from "./assets/cash.png";

interface IPay {
  idPaymentMethod: number;
  methodName: string;
  paymentIcon: string;
}

interface IPayment {
  paymentMethod: number;
  amount: string;
}

// Define the base URL as a constant
const BASE_URL = "http://localhost:3001";

// Reactive state to hold the input values from NumPad
const valueFromMoneyNumpad = ref<string>("");
const numberOfPeople = ref<string>("");

// State to hold payment methods
const paymentMethods = ref<IPay[]>([]);
const selectedPaymentMethod = ref<number | null>(null); // Track selected payment method

// Reactive state to hold the fetched payments
const payments = ref<IPayment[]>([]);

// Fetching payment methods from the API
const fetchPaymentMethods = async () => {
  try {
    const response = await fetch(`${BASE_URL}/payment-methods`);
    const data: IPay[] = await response.json();

    // Map fetched data to include image paths
    paymentMethods.value = data.map((method) => {
      let paymentIcon = "";
      switch (method.idPaymentMethod) {
        case 1:
          paymentIcon = cardImage;
          break;
        case 2:
          paymentIcon = cashIcon;
          break;
        case 3:
          paymentIcon = paypalIcon;
          break;
        default:
          paymentIcon = "path/to/default-icon.png"; // Fallback to a default icon
      }
      return { ...method, paymentIcon };
    });
    console.log("Payment methods fetched:", paymentMethods.value);
  } catch (error) {
    console.error("Error fetching payment methods:", error);
  }
};

const fetchPayments = async () => {
  try {
    const response = await fetch(`${BASE_URL}/tips`);
    const rawData = await response.json(); // Fetch the raw data
    console.log("Raw payments data:", rawData); // Log raw data

    // Map the fetched data to the IPayment structure
    payments.value = rawData.map((payment: any) => ({
      paymentMethod: payment.PaymentMethod_idPaymentMethod, // Map correctly
      amount: payment.amount,
    }));

    console.log("Payments fetched:", payments.value); // Log mapped payments
  } catch (error) {
    console.error("Error fetching payments:", error);
  }
};

// Call the fetch function to get payment methods and payments initially
fetchPaymentMethods();
fetchPayments();

// Function to handle the emitted input value (total tip)
const handleInputValue = (value: string) => {
  valueFromMoneyNumpad.value = value; // Store the total tip value
};

// Function to handle the emitted people count value
const handlePeopleCount = (value: string) => {
  numberOfPeople.value = value; // Store the number of people
};

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
  selectedPaymentMethod.value = id;
};

const distributeTips = async () => {
  const shiftId = 1; // DEFAULT SHIFT ID
  const totalTips = parseFloat(valueFromMoneyNumpad.value);
  const employeeCount = parseInt(numberOfPeople.value, 10);

  if (employeeCount <= 0) {
    console.error("Number of people must be greater than zero.");
    return;
  }

  const newTip = {
    amount: valueFromMoneyNumpad.value,
    paymentMethod: {
      idPaymentMethod: selectedPaymentMethod.value, // Ensure this is not null
    },
    shift: {
      idShift: 1, // Ensure this is not null
    },
  };

  console.log({
    amount: valueFromMoneyNumpad.value,
    paymentMethod: selectedPaymentMethod.value,
    shiftId: shiftId,
  });

  try {
    // Step 1: Add tip
    const addTipResponse = await fetch(`${BASE_URL}/tips/add`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(newTip),
    });

    const addedTip = await addTipResponse.json();
    if (!addTipResponse.ok) {
      console.error(addedTip.message); // Handle error adding tip
      return;
    }

    // Step 2: Distribute tips using the added Tip ID
    const response = await fetch(`${BASE_URL}/tips/distribute`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        shiftId,
        totalTips,
        employeeCount,
        Tip_idTip: addedTip.id, // Use the newly added tip ID
      }),
    });

    const result = await response.json();
    if (response.ok) {
      console.log(result.message); // Success message
      await fetchPayments(); // Fetch payments after distributing tips

      // Reset values after distribution
      valueFromMoneyNumpad.value = "";
      numberOfPeople.value = "";
      selectedPaymentMethod.value = null; // Reset selected payment method
    } else {
      console.error(result.message); // Error message
    }
  } catch (error) {
    console.error("Error distributing tips:", error);
  }
};

// New method to handle the button click
const handleDistributeTips = () => {
  distributeTips(); // Call distributeTips directly
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
              <span class="tipValue">$ {{ valueFromMoneyNumpad }}</span>
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
      <div class="right">
        <div class="rightTop">
          <h2 class="rightTitle">Pagos</h2>
        </div>
        <div class="rightBottom" v-if="payments.length === 0">Sin Pagos</div>
        <div
          class="rightBottom"
          v-else
          v-for="(payment, index) in payments"
          :key="index"
        >
          <Payment
            :image="
              payment.paymentMethod === 1
                ? cardImage
                : payment.paymentMethod === 2
                ? cashIcon
                : paypalIcon
            "
            :paymentMethod="payment.paymentMethod"
            :amount="payment.amount"
          />
        </div>
      </div>
    </div>
    <div class="mainBottom">
      <div class="bottomLeft">
        <div class="bottomLeftTop">
          <p class="totalTips">
            Total de propinas: <span>{{ finalCount }}</span>
          </p>
          <p class="remainingTips">
            Restante por pagar: <span>{{ valueFromMoneyNumpad }}</span>
          </p>
        </div>
        <div class="bottomLeftBottom"></div>
      </div>
      <div class="bottomRight">
        <button @click="handleDistributeTips">
          Pagar {{ finalCount }} en Propinas
        </button>
      </div>
    </div>
  </main>
</template>
