<template>
  <div class="lg:grid grid-cols-4 gap-5 py-4 md:py-10 px-4 max-w-[1200px] mx-auto">
    <div class="col-span-3">
      <component 
        :is="currentComponent" 
        @submit="onSubmit" 
        @goBack="getFirstStep" 
        :data="currentComponentData as UserData"
      />
    </div>
    <OrderDetails />
  </div>
</template>

<script setup lang="ts">
import { ref, shallowRef, computed, type Component } from 'vue'
import type { PaymentData, UserData } from './types'
import PersonalInformation from './components/PersonalInformation.vue'
import OrderDetails from './components/OrderDetails.vue'

const currentComponent = shallowRef<Component>(PersonalInformation)
const data = ref({
  userData: {
    firstName: '',
    lastName: '',
    email: '',
    country: '',
    postalCode: '',
    phoneNumber: '',
  },
  paymentData: {
    card: '',
    code: '',
    expDate: '',
  }
})
const currentComponentData = computed(() => {
  return currentComponent.value === PersonalInformation ? data.value.userData : data.value.paymentData
})

const onSubmit = async ($event: { step: string, data: UserData | PaymentData }) => {
  const step = $event.step
  if (step === 'personal-information') {
    const { default: PaymentsDetails } = await import('./components/PaymentsDetails.vue')
    currentComponent.value = PaymentsDetails
    data.value.userData = $event.data as UserData
  } else if (step === 'payments-details') {
    data.value.paymentData = $event.data as PaymentData
    console.log({ ...data.value.userData, ...data.value.paymentData })
  }
}

const getFirstStep = () => {
  currentComponent.value = PersonalInformation
}
</script>
