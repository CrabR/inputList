<template>
  <div class="input-list">
    <span class="label">{{ label }}</span>
    <div class="input-container">
      <el-form ref="form" :model="data">
        <div v-for="(item, index) in data" :key="index" class="input-content">
          <el-form-item :prop="'' + index" :rules="rules" :error="error(data[index], index)">
            <el-input ref="input" v-model="data[index]" @keypress.enter="add(index)" />
          </el-form-item>
          <div class="ops">
            <div :class="['ops-btn', canDel ? '' : 'op-btn__disabled']">
              <el-icon @click="del(index)">
                <Minus />
              </el-icon>
            </div>
            <div :class="['ops-btn', canAdd ? '' : 'op-btn__disabled']">
              <el-icon @click="add(index)">
                <Plus />
              </el-icon>
            </div>
          </div>
        </div>
      </el-form>
    </div>
  </div>
</template>

<script setup>
import { computed, nextTick, getCurrentInstance, ref, onBeforeMount } from "vue";
import { Plus, Minus } from "@element-plus/icons-vue";

const props = defineProps({
  modelValue: {
    type: Array,
    required: true,
  },
  label: {
    type: String,
  },
  rules: {
    type: [Array, Object],
  },
  max: {
    type: Number,
    default: 0,
  },
  error: {
    type: Function,
    default: () => "",
  },
});

const emits = defineEmits(["update:modelValue"]);

const data = computed({
  get: () => props.modelValue,
  set: (val) => emits("update:modelValue", val),
});

// const instance = getCurrentInstance();
const canAdd = computed(() => props.max === 0 || data.value.length < props.max);
const add = (index) => {
  if (canAdd.value) {
    data.value?.splice(index + 1, 0, "");
    nextTick(() => {
      focus(index + 1);
    });
  }
};

const canDel = computed(() => data.value.length > 1);
const del = (index) => {
  if (canDel.value) {
    data.value.splice(index, 1);
  }
};

onBeforeMount(() => {
  if (props.modelValue?.length === 0) {
    add(-1);
  }
});

const input = ref();
const focus = (index) => {
  if (!props.modelValue?.length) {
    return;
  }
  if (index === undefined) {
    index = (props.modelValue?.length ?? 0) - 1;
  }
  input.value?.[index]?.focus?.();
};

const form = ref();
const validate = (handler) => {
  return form.value?.validate?.(handler);
};

defineExpose({
  focus,
  validate,
});
</script>

<style scoped lang="less">
:deep(.el-form-item) {
  margin-right: 0;
  margin-bottom: 3px;
}
:deep(.el-form-item__content) {
  display: block;
}
:deep(.el-form-item__error) {
  position: unset;
}
.input-list {
  white-space: nowrap;
}
.input-container {
  display: inline-block;
}

.input-content {
  display: flex;
}

.label {
  line-height: 32px;
  vertical-align: top;
  margin-right: 10px;
}

.ops {
  display: inline-block;
  margin: 5px;

  .ops-btn {
    border: 2px solid #0080ff;
    border-radius: 20px;
    // box-shadow: 0 0 3px 2px rgba(0,128,255, 0.8);

    color: #0080ff;
    display: inline-block;
    /* padding: 10px; */
    height: 20px;
    width: 20px;
    text-align: center;
    vertical-align: middle;
    font-weight: bold;

    &.op-btn__disabled {
      color: #ccc;
      border-color: #ccc;
    }

    .el-icon {
      height: 20px;
      width: 20px;
    }

    & + .ops-btn {
      margin-left: 8px;
    }
  }
}
</style>
