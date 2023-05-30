<template>
  <div class="switch" :style="getStyle" :class="getClass">
    <label v-if="textPosition === 'left'" :for="`switch-button${id}`" class="text left"
      ><slot></slot
    ></label>
    <button
      :id="`switch-button${id}`"
      type="button"
      class="button"
      :class="{ on: on }"
      :aria-label="ariaLabel"
      :disabled="disabled"
      @click="click"
    ></button>
    <label v-if="textPosition === 'right'" :for="`switch-button${id}`" class="text right"
      ><slot></slot
    ></label>
  </div>
</template>

<script setup lang="ts">
  import type { CSSProperties } from 'vue';

  const { createId } = useCreateId();

  const id = createId();

  const props = defineProps({
    on: {
      type: Boolean,
      required: true,
    },
    textPosition: {
      type: String,
      required: false,
      default: undefined,
    },
    top: {
      type: String,
      required: false,
      default: undefined,
    },
    bottom: {
      type: String,
      required: false,
      default: undefined,
    },
    left: {
      type: String,
      required: false,
      default: undefined,
    },
    right: {
      type: String,
      required: false,
      default: undefined,
    },
    disabled: {
      type: Boolean,
      required: false,
      default: false,
    },
    ariaLabel: {
      type: String,
      required: false,
      default: 'switch',
    },
  });

  const getStyle = computed((): CSSProperties | undefined => {
    if (props.top || props.bottom || props.left || props.right) {
      return {
        position: 'absolute',
        top: props.top,
        bottom: props.bottom,
        left: props.left,
        right: props.right,
      };
    } else {
      return undefined;
    }
  });

  const getClass = computed(() => {
    return {
      disabled: props.disabled,
    };
  });

  const emit = defineEmits<{ (e: 'click', value: boolean): void }>();

  const click = () => {
    emit('click', props.on);
  };
</script>

<style lang="scss" scoped>
  .switch {
    display: flex;
    align-items: center;
    justify-content: flex-start;
    white-space: nowrap;

    &.disabled {
      pointer-events: none;
    }
  }

  .text {
    color: var(--gray8);
    font-weight: 400;
    font-size: var(--font-size-sm);
    cursor: pointer;
    &.left {
      padding-right: 10px;
    }
    &.right {
      padding-left: 10px;
    }
  }

  .button {
    position: relative;
    width: 40px;
    height: 21px;
    border-radius: 50px;
    background-color: var(--gray3);
    transition: background-color 0.18s;
    &::before {
      position: absolute;
      top: 3px;
      left: 3px;
      width: 15px;
      height: 15px;
      border-radius: 20px;
      background-color: var(--white);
      content: '';
      transition: left 0.18s;
    }
    &.on {
      background-color: var(--color5);
      &::before {
        left: 22px;
      }
    }

    .disabled & {
      background-color: var(--gray2);
      &::before {
        background-color: var(--gray0);
      }
      &.on {
        background-color: var(--color12);
      }
    }
  }
</style>