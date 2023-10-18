<template>
  <ez-select
      ref="select"
      class="input-group"
      :name="name"
      :disabled="disabled"
      :isFullWidth="isFullWidth"
      :value="selected"
      :selected="selected"
      :label="label"
      :options="categories"
      @change="onChange"
  >
    <ul>
      <li v-for="option in data" :key="option.id">
        <ez-option class="parent" :option="option">
          {{ option.name }}
        </ez-option>

        <ul v-if="option.children && option.children.length">
          <li v-for="child in option.children" :key="child.id">
            <ez-option :option="child">{{ child.name }}</ez-option>
          </li>
        </ul>
      </li>
    </ul>
  </ez-select>
</template>

<script>
import EzSelect, { EzOption } from '@/components/Select';

export default {
  components: {
    EzSelect,
    EzOption,
  },
  props: {
    data: {
      type: Array,
      required: true,
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
    name: {
      type: String,
      required: false,
    },
    isParentDisabled: {
      type: Boolean,
      default: false,
    },
    label: {
      type: String,
    },
  },
  computed: {
    categories() {
      let categories = [];
      for (let i = 0; i < this.data.length; i++) {
        const category = this.data[i];
        categories.push(category);
        for (let j = 0; j < category.children.length; j++) {
          categories.push(category.children[j]);
        }
      }
      return categories.sort(this.sortByName);
    },
  },
  methods: {
    sortByName(a, b) {
      const aName = a.name.toLowerCase();
      const bName = b.name.toLowerCase();
      return aName !== bName ? bName < aName ? -1 : 1 : 0;
    },
    onChange(value) {
      this.$emit("change", value);
    },
    reset() {
      this.$refs.select.reset();
    },
  },
};
</script>

<style scoped lang="scss">
:deep() .ez-select__display-container {
  height: 36px;
}
.ez-select__dropdown {
  li ul .ez-option {
    padding-left: 46px;
  }
}

.parent {
  font-weight: bold;
}
</style>
