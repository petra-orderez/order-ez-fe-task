<template>
  <div
      v-on="!disabled ? { click: toggle } : {}"
      ref="select"
      class="ez-select"
      :class="[
      { 'ez-select--full-width': isFullWidth },
      { 'ez-select--disabled': disabled },
    ]"
  >
    <label class="ez-select__label" :for="id" v-if="label">{{ label }}</label>
    <select class="ez-select__select" :disabled="disabled" :id="id">
      <option
          v-for="option in options"
          :key="option[valueField]"
          :value="option[valueField]"
          :selected="
          selectedOption && option[valueField] === selectedOption[valueField]
        "
      >
        {{ option[nameField] }}
      </option>
    </select>
    <div :class="['ez-select__display-container', { border: expanded }]">
      <slot name="display">
        <div
            :class="[
            'ez-select__display',
            { 'ez-select__display--placeholder': !selectedOption[valueField] },
          ]"
        >
          {{ selectedOption && selectedOption[nameField] }}
        </div>
        <img v-if="expanded" src="../../assets/angle-up-solid.svg" />
        <img v-else src="../../assets/angle-down-solid.svg" />
      </slot>
    </div>
    <div class="ez-select__dropdown-container">
      <div v-if="expanded" class="ez-select__dropdown">
        <ez-option
            v-for="option in options"
            :key="option[valueField]"
            :option="option"
            :selected="
              selectedOption &&
              option[valueField] === selectedOption[valueField]
            "
        >
          {{ option[nameField] }}
        </ez-option>
      </div>
    </div>
  </div>
</template>

<script>
import EzOption from "./EzOption.vue";
import { SELECT_OPTION } from "./constants";

export default {
  components: {
    EzOption,
  },
  props: {
    options: {
      type: Array,
      default: () => [],
    },
    valueField: {
      type: String,
      default: "id",
    },
    nameField: {
      type: String,
      default: "name",
    },
    value: {
      type: [Number, String, Object],
      default: () => null,
    },
    selected: {
      type: [Number, String],
      required: false,
    },
    disabled: {
      type: Boolean,
      default: false,
    },
    isFullWidth: {
      type: Boolean,
      default: false,
    },
    label: {
      type: String,
    },
  },
  provide() {
    return {
      [SELECT_OPTION]: this.setValue,
    };
  },
  data() {
    return {
      id: null,
      expanded: false,
      selectedOption: null,
      originalValue: null,
    };
  },
  methods: {
    toggle() {
      this.expanded = !this.expanded;
    },
    setValue(option, emitEvent = true) {
      this.selectedOption = option;

      if (emitEvent) {
        this.$emit("change", this.selectedOption);
      }
    },
    reset() {
      this.setValue(this.originalValue, false);
    },
    documentClick(e) {
      if (!this.$refs.select.contains(e.target)) {
        this.expanded = false;
      }
    },
    onCreated() {
      const firstOption = this.options.length ? this.options[0] : null;
      const value =
          typeof this.value === "object"
              ? this.value
              : this.options.find((o) => o[this.valueField] === this.value);
      this.selectedOption = value || firstOption;
      this.originalValue = this.selectedOption;
    },
  },
  watch: {
    selected() {
      const firstOption = this.options.length ? this.options[0] : null;
      const value =
          typeof this.selected === "object"
              ? this.selected
              : this.options.find((o) => o[this.valueField] === this.selected);

      this.selectedOption = value || firstOption;
      this.originalValue = this.selectedOption;
    },
    options() {
      this.onCreated();
    },
  },
  created() {
    this.onCreated();
  },
  mounted() {
    this.id = "testId";
    window.addEventListener("click", this.documentClick);
  },
  beforeDestroy() {
    window.removeEventListener("click", this.documentClick);
  },
};
</script>

<style scoped lang="scss">
$input-height: 36px;
$input-width: 360px;
$border-radius: 3px;
$border-width: 2px;
$padding-x: 12px;
$padding-y: 0;

$dropdown-shadow: 0 8px 6px -4px rgba(0, 0, 0, 0.12);
$dropdown-top: 8px;
$dropdown-padding-x: 0;
$dropdown-padding-y: 4px;

$option-padding-x: 16px;
$option-padding-y: 0;

$label-font-size: 12px;
$label-line-height: 14px;
$label-letter-spacing: 0.4px;
$label-color: #6c7995;

$color-gray-F5: #f5f6fa;
$color-gray-b4: #b4b8c2;
$color-hover: #e9ebf2;
$color-border: #dee1e4;
$selected-border-color: #4d7cfe;

.ez-select {
  width: $input-width;
  line-height: $input-height;
  box-sizing: content-box;
  cursor: pointer;

  &--full-width {
    width: 100%;
  }

  ul {
    margin: 0;
    padding: 0;
    list-style: none;

    li {
      margin: 0;
    }
  }

  &__select {
    display: none;
  }

  &__label {
    display: block;
    margin-bottom: 6px;
    color: $label-color;
    font-size: $label-font-size;
    line-height: $label-line-height;
    font-weight: 500;
    text-transform: capitalize;
  }

  &__dropdown {
    position: absolute;
    top: $dropdown-top;
    max-height: 320px;
    overflow-y: auto;
    border: 1px solid $color-border;
    background-color: #ffffff;
    border-radius: $border-radius;
    padding: $dropdown-padding-y $dropdown-padding-x;
    box-shadow: $dropdown-shadow;
    width: 100%;
    box-sizing: content-box;
  }

  &__dropdown-container {
    position: relative;
    z-index: 1000;
  }

  &__display-container {
    display: flex;
    align-items: center;
    justify-content: space-between;

    background-color: $color-gray-F5;
    padding: $padding-y $padding-x;
    border-radius: $border-radius;
    height: 100%;

    &:hover {
      background-color: $color-hover;
      transition: background-color 0.2s ease-in-out;
    }

    img {
      height: 16px;
    }
  }

  &__display {
    font-size: 14px;
    line-height: 16px;
    padding: 8px 0;
    border-radius: $border-radius;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow-x: hidden;

    &--placeholder {
      color: $label-color;
    }
  }

  :deep() .ez-option {
    padding: $option-padding-y $option-padding-x;
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow-x: hidden;

    &:hover {
      background-color: $color-gray-b4;
    }

    &--disabled {
      cursor: default;
      opacity: 0.5;

      &:hover {
        background-color: transparent;
      }
    }
  }

  &--disabled {
    pointer-events: none;

    .ez-select__display-container,
    .ez-select__display {
      color: $color-gray-b4;
    }
  }
}

.border {
  box-shadow: 0 0 0 $border-width $selected-border-color;
  border-radius: $border-radius;
}
</style>
