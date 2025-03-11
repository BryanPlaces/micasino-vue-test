<template>
  <div class="options-wrapper">
    <div class="select-option">
      <span>Available Options</span>
      
      <select :name="field.id" :disabled="field.disabled ?? null" multiple="true">
        <option
          v-for="option in enabledOptions"
          @click="toggleOption(option.id, 'enabled')"
          :key="option.value"
          :value="option.id"
        >
          {{option.label}}
        </option>
      </select>
    </div>

    <div class="select-option">
      <span>Disable Options</span>

      <select :name="`${field.id}-2`" :disabled="field.disabled ?? null" multiple="true">
        <option
          v-for="option in disabledOptions"
          :key="option.value"
          @click="toggleOption(option.id, 'disabled')"
          :value="option.value"
        >
          {{option.label}}
        </option>
      </select>
    </div>
  </div>
</template>

<script setup>
  import { ref } from 'vue';
  import fieldMixin from '../FieldMixin';

  const emit = defineEmits(['update']);
  const props = defineProps({
    field: {
      type: Object,
      required: true,
    },
    modelValue: {
      type: [String, Number, Boolean, Object, Array],
      default: '',
    },
  });

  const enabledOptions = ref(props.field.options);
  const disabledOptions = ref([]);

  const toggleOption = (idOption, from) => {
    const sourceArray = from === 'enabled' ? enabledOptions : disabledOptions;
    const targetArray = from === 'enabled' ? disabledOptions : enabledOptions;

    const optionIndex = sourceArray.value.findIndex(option => option.id === idOption);
    if (optionIndex !== -1) {
      const [option] = sourceArray.value.splice(optionIndex, 1);
      targetArray.value.push(option);
      updateData();
    }
  }

  const updateData = () => {
    const data = {
      id: props.field.id,
      value: enabledOptions.value.map(option => option.id),
    }

    emit('update', data);
  };

  updateData()
</script>

<style scoped>
  .options-wrapper {
    width: 100%;
    display: flex;
    gap: 1.5rem;
    margin: 1rem 0;
  }

  .options-wrapper .select-option {
    width: 100%;
    display: flex;
    flex-direction: column;
    gap: 0.3rem;
  }

  .select-option select {
    padding: 2px 10px;
    min-height: 150px;
    overflow-y: auto;
  }
</style>
