<template>
  <div
    class="ui-textarea"
    v-bind="attrs"
  >
    <textarea
      v-keyboard-focus
      v-bind="defaultProps.textareaAttrs"
      :value="modelValue"
      :style="{ resize: resizeValue }"
      class="ui-textarea__textarea"
      @input="inputHandler($event)"
    />
  </div>
</template>

<script lang="ts">
export default { inheritAttrs: false };
</script>

<script setup lang="ts">
import { computed } from 'vue';
import type { TextareaHTMLAttributes } from 'vue';
import useAttributes from '../../../composable/useAttributes';
import { keyboardFocus as vKeyboardFocus } from '../../../utilities/directives';
import type { DefineAttrsProps } from '../../../types';

export type TextareaModelValue = string;
export interface TextareaProps {
  /**
   * Use this props or v-model to set value.
   */
  modelValue?: TextareaModelValue;
  /**
   * Use this props to enable resizing on textarea.
   * true - both direction resizing, false - disable resizing,
   * 'horizontal' - horizontal resizing only, 'vertical' - vertical resizing only
   */
  resize?: boolean | 'horizontal' | 'vertical';
  /**
   * Use this props to set input placeholder.
   */
  placeholder?: string;
  /**
   * Use this props to disabled textarea.
   * Remember to use `ui-textarea--is-disabled` class to style disabled textarea.
   */
  disabled?: boolean;
  /**
   * Use this props to pass attrs for textarea element.
   */
  textareaAttrs?: DefineAttrsProps<null, TextareaHTMLAttributes>;
}
export type TextareaAttrsProps = DefineAttrsProps<TextareaProps>;
export interface TextareaEmits {
  (e:'update:modelValue', value: TextareaModelValue): void
}

const props = withDefaults(defineProps<TextareaProps>(), {
  modelValue: '',
  resize: false,
  placeholder: '',
  disabled: false,
  textareaAttrs: () => ({}),
});
const defaultProps = computed(() => ({
  textareaAttrs: {
    placeholder: props.placeholder,
    disabled: props.disabled,
    ...listeners.value,
    ...props.textareaAttrs,
  },
}));
const emit = defineEmits<TextareaEmits>();
const {
  attrs, listeners,
} = useAttributes();
const inputHandler = (event: Event) => {
  const el = event.target as HTMLInputElement;
  emit('update:modelValue', el.value);
};
const resizeValue = computed(() => {
  if (typeof props.resize !== 'boolean') {
    return props.resize;
  }
  return props.resize ? 'both' : 'none';
});
</script>

<style lang="scss">
@use "../../../styles/functions";
@use "../../../styles/mixins";

.ui-textarea {
  $this: &;
  $element: textarea;

  @include mixins.inner-border($element, $radius: var(--border-radius-form));

  display: flex;
  align-items: center;
  justify-content: center;
  transition: border-color 150ms ease-in-out;

  @include mixins.hover {
    &::after {
      @include mixins.use-logical($element + "-hover", border-color, var(--color-border-strong-hover));
    }
  }

  &:focus-within {
    box-shadow: var(--focus-outer);
  }

  &__textarea {
    @include mixins.font($element, body-1);
    @include mixins.use-logical($element, padding, var(--space-12) var(--space-16));
    @include mixins.use-logical($element, border, 0);

    flex: 1;
    background: transparent;
    border-radius: inherit;
    caret-color: functions.var($element, caret-color, var(--color-blue-500));
    color: functions.var($element, color, var(--color-text-body));
    outline: none;

    &::placeholder {
      color: functions.var($element + "-placeholder", color, var(--color-text-dimmed));
      font: inherit;
      letter-spacing: inherit;
    }
  }

  &--is-disabled {
    &::after {
      @include mixins.use-logical($element, border-color, var(--color-border-subtle));
    }

    @include mixins.hover {
      &::after {
        @include mixins.use-logical($element, border-color, var(--color-border-subtle));
      }
    }

    #{$this}__textarea {
      caret-color: functions.var($element, caret-color, var(--color-gray-400));
      color: functions.var($element, color, var(--color-text-disabled));
      cursor: not-allowed;

      &::placeholder {
        color: functions.var($element + "-placeholder", color, var(--color-text-disabled));
      }
    }
  }

  &--has-error {
    &::after {
      @include mixins.use-logical($element, border-color, var(--color-border-error-strong));
    }

    @include mixins.hover {
      &::after {
        @include mixins.use-logical($element + "-hover", border-color, var(--color-border-error-strong-hover));
      }
    }
  }
}
</style>
