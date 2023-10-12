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
      <input 
        :type="type"
        :name="name" 
        :placeholder="placeholder"
        v-model="value"
        class="pt-4 pb-2 px-2 rounded shadow-sm text-slate-400 w-full"
        :class="{ 'border border-red-500': errors?.length }"
        @input="emit('input', value)" 
        @focus="inputFocused = true"
        @blur="inputFocused = false"
      />
      <div class="absolute right-2 top-1/2 -translate-y-1/2 w-16">
        <slot name="icon" class="absolute" />
      </div>
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
import { ErrorObject } from "@vuelidate/core"

const props = defineProps<{
  label: string
  name: string
  value: string
  placeholder?: string
  type?: string
  errors?: ErrorObject[]
}>()

const emit = defineEmits(['input'])

const value = ref(props.value)
const inputFocused = ref(false)
const isLabelTranslated = computed(() => inputFocused.value || value.value || props.placeholder)
</script>
