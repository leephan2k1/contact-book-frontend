<template>
  <div class="input-group">
    <input
      type="text"
      class="form-control"
      placeholder="Nhập thông tin cần tìm"
      :value="modelValue"
      @input="updateModelValue"
      @keyup.enter="submit"
    />
    <div class="input-group-append">
      <button class="btn btn-outline-secondary" type="button" @click="submit">
        <i class="fas fa-search"></i> Tìm kiếm
      </button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    modelValue: { type: String, default: "" },
  },
  setup(_, { emit }) {
    const submit = () => {
      emit("submit");
    };

    const updateModelValue = (e) => {
      emit("update:modelValue", e.target.value);
    };

    return { updateModelValue, submit };
  },
};
</script>
