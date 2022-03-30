<template>
  <ul class="list-group">
    <li
      class="list-group-item"
      v-for="(contact, index) in contacts"
      :key="contact.id"
      :class="{ active: index === selectedIndex }"
      @click="select(index)"
    >
      {{ contact.name }}
    </li>
  </ul>
</template>

<script>
import { watch, ref } from "vue";

export default {
  props: {
    contacts: { type: Array, default: [] },
    activeIndex: { type: Number, default: -1 },
  },
  methods: {
    select(index) {
      this.selectedIndex = index;
      this.$emit("update:activeIndex", index);
    },
  },

  setup(props, { emit }) {
    const selectedIndex = ref(props.activeIndex);

    watch(
      () => props.activeIndex,
      (newValue) => {
        if (newValue === -1) {
          this.selectedIndex = -1;
        }
      }
    );

    const select = (index) => {
      selectedIndex.value = index;
      emit("update:activeIndex", index);
    };

    return { select, selectedIndex };
  },
};
</script>
