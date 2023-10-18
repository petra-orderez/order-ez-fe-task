<template>
  <div
      :class="[
        'ez-option',
        { 'ez-option--selected': selected },
        { 'ez-option--disabled': disabled }
      ]"
      @click="!disabled && select(option)">
    <slot></slot>
  </div>
</template>

<script>
import { SELECT_OPTION } from './constants';

export default {
  props: {
    option: {
      type: Object,
      required: true,
      default: () => ({}),
    },
    selected: {
      type: Boolean,
      default: false,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
  },
  inject: {
    select: SELECT_OPTION,
  },
};
</script>
