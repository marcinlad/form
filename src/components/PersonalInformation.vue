<template>
  <div>
    <p class="font-bold mb-4 xl:-ml-8 uppercase">
      <span class="bg-black text-white inline-block w-5 h-5 rounded-full text-center leading-5 mr-2">1</span>
      Personal information
    </p>
    <div class="md:grid grid-cols-2 gap-4">
      <BaseInput
        name="first-name"
        label="First name"
        :value="userData.firstName"
        @input="updateValue('firstName', $event)"
        :errors="vuelidate.firstName.$errors"
      />
      <BaseInput
        name="last-name"
        label="Last name"
        :value="userData.lastName"
        @input="updateValue('lastName', $event)"
        :errors="vuelidate.lastName.$errors"
      />
    </div>
    <BaseInput
      name="email"
      label="Email"
      type="email"
      :value="userData.email"
      @input="updateValue('email', $event)"
      :errors="vuelidate.email.$errors"
    />
    <div class="md:grid grid-cols-2 gap-4">
      <BaseSelect
        name="country"
        label="Country"
        :value="userData.country"
        :options="['United States', 'Canada', 'United Kingdom', 'Germany', 'France', 'Japan']"
        @change="updateValue('country', $event)"
        :errors="vuelidate.country.$errors"
      />
      <BaseInput
        name="postal-code"
        label="Postal code"
        :value="userData.postalCode"
        @input="updateValue('postalCode', $event)"
        :errors="vuelidate.postalCode.$errors"
      />
    </div>
    <BaseInput
      name="phone-number"
      label="Phone number"
      placeholder="(123) 123-45-67"
      :value="userData.phoneNumber"
      @input="updateValue('phoneNumber', $event)"
      :errors="vuelidate.phoneNumber.$errors"
    />
    <button 
      class="button bg-green-500 text-white w-full font-semibold uppercase px-5 py-3 rounded shadow-sm
      hover:bg-green-700 duration-300"
      @click="onSubmit"
    >
      Next
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref, PropType } from 'vue'
import useVuelidate from '@vuelidate/core'
import { required, minLength, email } from '@vuelidate/validators'
import type { UserData } from '../types'
import BaseInput from './BaseInput.vue'
import BaseSelect from './BaseSelect.vue'

const props = defineProps({
  data: {
    type: Object as PropType<UserData>,
    required: true,
  }
})

const emit = defineEmits(['submit'])
const userData = ref({
  firstName: props.data.firstName,
  lastName: props.data.lastName,
  email: props.data.email,
  country: props.data.country,
  postalCode: props.data.postalCode,
  phoneNumber: props.data.phoneNumber,
})

const vuelidate = useVuelidate({
  firstName: { required, minLength: minLength(3) },
  lastName: { required, minLength: minLength(3) },
  email: { required, email },
  country: { required },
  postalCode: { required, minLength: minLength(5) },
  phoneNumber: { required, minLength: minLength(10) }
}, userData)

const updateValue = (key: keyof typeof userData.value, event: string) => {
  userData.value[key] = event
  vuelidate.value[key].$validate()
}

const onSubmit = () => {
  vuelidate.value.$touch()
  if (vuelidate.value.$error) return
  emit('submit', { data: userData.value, step: 'personal-information' })
}
</script>
