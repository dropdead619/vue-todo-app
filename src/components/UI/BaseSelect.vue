<script setup>

const props = defineProps({
  modelValue: {
    type: String,
    default: '',
  },
  data: {
    type: Array,
    required: true,
  },
  optClass: {
    type: String,
    default: '',
  },
});

const emit = defineEmits(['update:modelValue']);
</script>

<template>
  <select
    class="select"
    :value="props.modelValue"
    @input="emit('update:modelValue', $event.target.value)">
    <option
      v-for="option in props.data"
      :key="option.value"
      :class="props.optClass"
      :value="option.value">
      {{ option.label }}
    </option>
  </select>
</template>
