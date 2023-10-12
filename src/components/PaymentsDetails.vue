<template>
  <div>
    <p class="font-bold mb-4 xl:-ml-8 uppercase">
      <span class="bg-black text-white inline-block w-5 h-5 rounded-full text-center leading-5 mr-2">2</span>
      Payment details
    </p>
    <BaseInput
      name="credit-card-number"
      label="Credit Card Number"
      :value="paymentData.card"
      @input="updateValue('card', $event); updateCardType()"
      :errors="vuelidate.card.$errors"
    >
      <template #icon>
        <img v-if="cardType" :src="`/${cardType}.svg`" />
      </template>
    </BaseInput>
    <div class="md:grid grid-cols-2 gap-4">
      <BaseInput
        name="code"
        label="Security code"
        type="password"
        :value="paymentData.code"
        @input="updateValue('code', $event)"
        :errors="vuelidate.code.$errors"
      />
      <BaseInput
        name="date"
        label="Expiration date"
        :value="paymentData.expDate"
        @input="updateValue('expDate', $event)"
        :errors="vuelidate.expDate.$errors"
      />
    </div>
    <button 
      class="button bg-green-500 text-white w-full font-semibold uppercase px-5 py-3 rounded shadow-sm
      hover:bg-green-700 duration-300"
      @click="onSubmit"
    >
      Complete purchase
    </button>
    <a 
      href="#" 
      class="previous text-sm text-gray-500 mt-2 block hover:font-semibold text-center" 
      @click.prevent="goBack()"
    >
      previous step
    </a>
  </div>
</template>

<script setup lang="ts">
import { ref, PropType, Ref } from 'vue'
import useVuelidate from '@vuelidate/core'
import { required, minLength, maxLength, numeric } from '@vuelidate/validators'
import type { PaymentData } from '../types'
import BaseInput from './BaseInput.vue'

const props = defineProps({
  data: {
    type: Object as PropType<PaymentData>,
    required: true,
  }
})

const emit = defineEmits(['submit', 'goBack'])
const cardType: Ref<null | string> = ref(null)
const paymentData = ref({
  card: props.data.card,
  code: props.data.code,
  expDate: props.data.expDate,
})

const vuelidate = useVuelidate({
  card: { required, minLength: minLength(16), maxLength: maxLength(16), numeric },
  code: { required, minLength: minLength(3), numeric },
  expDate: { required, minLength: minLength(5) },
}, paymentData)

const updateCardType = () => {
  const cardsTypes = ['visa', 'mastercard']
  const randomIndex = Math.floor(Math.random() * cardsTypes.length)
  cardType.value = cardsTypes[randomIndex]
}

const updateValue = (key: keyof typeof paymentData.value, event: string) => {
  paymentData.value[key] = event
  vuelidate.value[key].$validate()
}

const onSubmit = () => {
  vuelidate.value.$touch()
  if (vuelidate.value.$error) return
  emit('submit', { step: 'payments-details', data: paymentData.value })
}

const goBack = () => {
  emit('goBack')
}
</script>

<style scoped lang="scss">
.previous {
  &:before {
    @apply content-['←'] mr-2 text-[26px];
  }
}
</style>
