<template>
  <div style="max-width: 300px;">
    <va-select
      v-model="value"
      class="mb-6"
      label="Readonly"
      :options="options"
      readonly
    />
    <va-select
      v-model="value"
      class="mb-6"
      label="Disabled"
      :options="options"
      disabled
    />
    <va-select
      v-model="value"
      class="mb-6"
      label="Loading"
      :options="options"
      loading
    />
    <va-select
      v-model="value"
      class="mb-6"
      label="Error state"
      :options="options"
      error
    />
    <va-select
      v-model="value"
      class="mb-6"
      label="Error state with messages"
      :options="options"
      error
      :error-messages="['Error message']"
    />
    <va-select
      v-model="value"
      class="mb-6"
      label="Success state"
      :options="options"
      success
    />
    <va-select
      v-model="value"
      label="Success state with messages"
      :options="options"
      success
      :messages="['Success message']"
    />
  </div>
</template>

<script>
export default {
  name: "State",
  data() {
    return {
      options: [
        "one",
        "two",
        "three",
        "four",
        "five",
        "six",
        "seven",
        "eight",
        "nine",
      ],
      value: "two",
    };
  },
};
</script>
