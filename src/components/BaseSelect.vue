<template>
  <div class="mb-4">
    <div class="w-full relative">
      <label 
        :for="name" 
        class="absolute left-2 top-1/2 -translate-y-1/2 text-[12px] font-semibold duration-300 pointer-events-none"
        :class="{ '!top-0 !translate-y-0': isLabelTranslated }"
      >
        {{ label }}
      </label>
      <select 
        :type="type"
        :name="name"
        v-model="value"
        class="pt-4 pb-2 px-2 rounded shadow-sm text-slate-400 w-full bg-white"
        :class="{ 'border border-red-500': errors?.length }"
        @change="emit('change', value)" 
      >
        <option v-if="placeholder" disabled selected>{{ placeholder }}</option>
        <option v-for="(option, index) in options" :key="index">
          {{ option }}
        </option>
      </select>
    </div>
    <div class="text-[12px] text-red-500">
      <div v-for="(error, index) in errors" :key="index">
        {{ error.$message }}
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
import { ErrorObject } from '@vuelidate/core'

const props = defineProps<{
  label: string
  name: string
  value: string
  options: string[]
  placeholder?: string
  type?: string
  errors?: ErrorObject[]
}>()

const emit = defineEmits(['change'])
const value = ref(props.value)
const isLabelTranslated = computed(() => value.value || props.placeholder)
</script>
