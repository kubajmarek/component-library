<template>
  <UiListItem
    :list-item-attrs="defaultProps.listItemAttrs"
    :class="[
      'ui-multiple-choices-item__content', {
        'ui-list-item--has-error': invalid,
        'ui-multiple-choices-item--has-error': invalid
      }
    ]"
    :tag="tag"
    :aria-labelledby="multipleChoicesItemId"
    role="radiogroup"
  >
    <!-- @slot Use this slot to replace legend template. -->
    <slot
      v-bind="{ label, }"
      name="legend"
    >
      <legend class="visual-hidden">
        {{ label }}
      </legend>
    </slot>
    <!-- @slot Use this slot to replace header template.-->
    <slot
      v-bind="{
        id: multipleChoicesItemId,
        textLabelAttrs,
        label,
        hasInfo,
        buttonInfoAttrs,
        iconInfoAttrs: defaultProps.iconInfoAttrs,
        translation,
      }"
      name="header"
    >
      <div class="ui-multiple-choices-item__header">
        <slot
          v-bind="{
            multipleChoicesItemId,
            textLabelAttrs,
            label
          }"
          name="label"
        >
          <UiText
            :id="multipleChoicesItemId"
            v-bind="textLabelAttrs"
            class="ui-multiple-choices-item__label"
          >
            {{ label }}
          </UiText>
        </slot>
        <slot
          name="info"
          v-bind="{
            hasInfo,
            buttonInfoAttrs,
            iconInfoAttrs: defaultProps.iconInfoAttrs,
            labelInfoAttrs,
            translation
          }"
        >
          <UiButton
            v-if="hasInfo"
            v-bind="buttonInfoAttrs"
            class="ui-button--text ui-button--small ui-multiple-choices-item__info"
          >
            <UiIcon
              v-bind="defaultProps.iconInfoAttrs"
              class="ui-button__icon ui-multiple-choices-item__info-icon"
            />
            <span
              v-bind="labelInfoAttrs"
              class="ui-multiple-choices-item__info-label"
            >
              {{ translation.info }}
            </span>
          </UiButton>
        </slot>
      </div>
    </slot>
    <div class="ui-multiple-choices-item__choices">
      <!-- TODO: create MultipleChoicesItemOptions component -->
      <UiList
        class="ui-multiple-choices-item__options"
      >
        <template
          v-for="(option, key) in optionsToRender"
          :key="key"
        >
          <!-- @slot Use this slot to replace option template.-->
          <slot
            name="option"
            v-bind="{
              value,
              listOptionItemAttrs: defaultProps.listOptionItemAttrs,
              invalid,
              option,
            }"
          >
            <UiListItem
              v-model="value"
              v-bind="option"
              :tag="UiRadio"
              :class="[
                'ui-multiple-choices-item__option-content', {
                  'ui-radio--has-error': invalid,
                  'ui-list-item--has-error': invalid,
                }
              ]"
              :list-item-attrs="defaultProps.listOptionItemAttrs"
            >
              {{ option.label }}
            </UiListItem>
          </slot>
        </template>
      </UiList>
      <!-- @slot Use this slot to replace alert template. -->
      <slot
        name="alert"
        v-bind="{
          invalid,
          alertAttrs,
          translation: defaultProps.translation,
        }"
      >
        <UiAlert
          v-if="invalid"
          v-bind="alertAttrs"
          class="ui-multiple-choices-item__alert"
        >
          {{ defaultProps.translation.invalid }}
        </UiAlert>
      </slot>
    </div>
  </UiListItem>
</template>

<script setup lang="ts">
import { computed } from 'vue';
import { uid } from 'uid/single';
import type {
  MultipleChoicesModelValue,
  MultipleChoicesOption,
} from '../UiMultipleChoices.vue';
import UiAlert from '../../../molecules/UiAlert/UiAlert.vue';
import type { AlertAttrsProps } from '../../../molecules/UiAlert/UiAlert.vue';
import UiButton from '../../../atoms/UiButton/UiButton.vue';
import type { ButtonAttrsProps } from '../../../atoms/UiButton/UiButton.vue';
import UiIcon from '../../../atoms/UiIcon/UiIcon.vue';
import type { IconAttrsProps } from '../../../atoms/UiIcon/UiIcon.vue';
import UiList from '../../UiList/UiList.vue';
import UiListItem from '../../UiList/_internal/UiListItem.vue';
import type { ListItemAttrsProps } from '../../UiList/_internal/UiListItem.vue';
import UiRadio from '../../../atoms/UiRadio/UiRadio.vue';
import UiText from '../../../atoms/UiText/UiText.vue';
import type { TextAttrsProps } from '../../../atoms/UiText/UiText.vue';
import type {
  DefineAttrsProps,
  HTMLTag,
  Icon,
} from '../../../../types';

export interface MultipleChoicesItemTranslation {
  info?: string;
  invalid?: string;
}
export interface MultipleChoicesItemProps {
  /**
   * Use this props to set multiple choices item tag.
   */
  tag?: HTMLTag;
  /**
   * Use this props to set multiple choices item label.
   */
  label?: string;
  /**
   * Use this props to set invalid state of choice item.
   */
  invalid?: boolean;
  /**
   *  Use this props or v-model to set checked.
   */
  modelValue?: MultipleChoicesModelValue;
  /**
   * Use this props to set item of item.
   */
  id?: string;
  /**
   * Use this props to pass labels inside component translation.
   */
  translation?: MultipleChoicesItemTranslation;
  /**
   *  Use this props to pass options.
   */
  options?: MultipleChoicesOption[];
  /**
   * Use this props to pass attrs for hint UiAlert
   */
  alertAttrs?: AlertAttrsProps;
  /**
   * Use this props to pass attrs for info UiButton.
   */
  buttonInfoAttrs?: ButtonAttrsProps;
  /**
   * Use this props to pass attrs for info label element.
   */
  labelInfoAttrs?: DefineAttrsProps<null>;
  /**
   * Use this props to pass attrs for info UiIcon.
   */
  iconInfoAttrs?: IconAttrsProps;
  /**
   * Use this props to pass attrs for label UiText.
   */
  textLabelAttrs?: TextAttrsProps;
  /**
   * Use this props to pass attrs for list item.
   */
  listItemAttrs?: ListItemAttrsProps;
  /**
   * Use this props to pass attrs for list option item.
   */
  listOptionItemAttrs?: ListItemAttrsProps;
}
export type MultipleChoicesItemAttrsProps = DefineAttrsProps<MultipleChoicesItemProps, ListItemAttrsProps>;
export interface MultipleChoicesItemEmits {
  (e: 'update:modelValue', value: MultipleChoicesModelValue): void;
}

const props = withDefaults(defineProps<MultipleChoicesItemProps>(), {
  tag: 'fieldset',
  label: '',
  invalid: true,
  modelValue: '',
  id: '',
  translation: () => ({
    info: 'What does it mean?',
    invalid: 'Please select one answer',
  }),
  options: () => ([]),
  alertAttrs: () => ({}),
  buttonInfoAttrs: () => ({}),
  labelInfoAttrs: () => ({}),
  iconInfoAttrs: () => ({ icon: 'info' }),
  textLabelAttrs: () => ({ tag: 'span' }),
  listItemAttrs: () => ({ class: 'ui-multiple-choices-item' }),
  listOptionItemAttrs: () => ({ class: 'ui-multiple-choices-item__option' }),
});
const defaultProps = computed(() => {
  const icon: Icon = 'info';
  const tag: HTMLTag = 'span';
  return {
    translation: {
      info: 'What does it mean?',
      invalid: 'Please select one answer',
      ...props.translation,
    },
    listItemAttrs: {
      class: 'ui-multiple-choices-item',
      ...props.listItemAttrs,
    },
    textLabelAttrs: {
      tag,
      ...props.textLabelAttrs,
    },
    iconInfoAttrs: {
      icon,
      ...props.iconInfoAttrs,
    },
    listOptionItemAttrs: {
      class: 'ui-multiple-choices-item__option',
      ...props.listOptionItemAttrs,
    },
  };
});
const emit = defineEmits<MultipleChoicesItemEmits>();
const multipleChoicesItemId = computed(() => (props.id || `multiple-choices-item-${uid()}`));
const value = computed({
  get: () => props.modelValue,
  set: (newValue) => {
    emit('update:modelValue', newValue);
  },
});
const hasInfo = computed(() => (Object.keys(props.buttonInfoAttrs).length > 0));
const optionsToRender = computed(() => props.options.map((option) => ({
  ...option,
  inputAttrs: {
    name: multipleChoicesItemId.value,
    ...option.inputAttrs,
  },
})));
</script>

<style lang="scss">
@use "../../../../styles/functions";
@use "../../../../styles/mixins";

.ui-multiple-choices-item {
  $this: &;
  $element: multiple-choices-item;

  &:not(:first-of-type) {
    @include mixins.override-logical(list-item, null, border-width, 0);
  }

  @include mixins.from-tablet {
    &:not(:first-of-type) {
      @include mixins.override-logical(list-item, $element, border-width);
    }
  }

  &__content {
    @at-root fieldset#{&} {
      border: none;
      margin: 0;
    }

    @include mixins.override-logical(list-item-content, $element + "-content", padding, 0);

    --list-item-content-hover-background: #{functions.var($element + "-content-hover", background, transparent)};

    display: block;

    @include mixins.from-tablet {
      --list-item-content-hover-background: #{functions.var($element + "-tablet-content-hover", background)};

      display: flex;
      gap: functions.var($element + "-tablet-content", gap, var(--space-24));
    }
  }

  &__header {
    @include mixins.use-logical($element + "-header", padding, var(--space-12) var(--space-20));
    @include mixins.use-logical($element + "-header", margin, var(--space-20) 0 0 0);

    display: flex;
    justify-content: space-between;
    gap: functions.var($element + "-header", gap, var(--space-12));

    @include mixins.from-tablet {
      @include mixins.use-logical($element + "-tablet-header", padding, 0);
      @include mixins.use-logical($element + "-tablet-header", margin, 0);

      flex-direction: column;
      align-items: flex-start;
      gap: functions.var($element + "-tablet-header", gap, var(--space-8));
    }
  }

  &__info {
    gap: functions.var($element + "-info", gap, var(--space-4));
  }

  &__info-label {
    @include mixins.to-mobile {
      position: absolute;
      overflow: hidden;
      width: 1px;
      height: 1px;
      clip: rect(0 0 0 0);
      clip-path: inset(50%);
      white-space: nowrap;
    }
  }

  &__info-icon {
    --button-icon-margin-logical: 0;
  }

  &__choices {
    display: flex;
    flex-direction: column;
    flex: 0;
    gap: functions.var($element + "-choices", gap, 0);

    @include mixins.from-tablet {
      gap: functions.var($element + "-tablet-choices", gap, var(--space-8));
    }
  }

  &__options {
    white-space: nowrap;

    @include mixins.from-tablet {
      display: flex;
      gap: functions.var($element + "-options", gap, var(--space-24));
    }
  }

  &__option {
    @include mixins.override-logical(list-item, $element + "-option", border-width,  1px 0 0 0);

    --radio-gap: #{functions.var($element + '-option', gap, var(--space-12 ))};

    &:last-of-type {
      @include mixins.override-logical(list-item, $element + "-option", border-width,  1px 0);
    }

    @include mixins.from-tablet {
      @include mixins.override-logical(list-item, $element + "-tablet-option", border-width, 0);

      --radio-gap: #{functions.var($element + '-tablet-option', gap, var(--space-8 ))};

      &:last-of-type {
        @include mixins.override-logical(list-item, $element + "-tablet-option", border-width, 0);
      }
    }
  }

  &__option-content {
    @include mixins.override-logical(list-item-content, $element + "-option-content", padding, var(--space-12) var(--space-20));
    @include mixins.override-logical(list-item-tablet-content, $element + "-tablet-option-content", padding, 0);

    --list-item-content-hover-background: #{functions.var($element + "-content-hover", background)};
  }

  &__alert {
    @include mixins.use-logical($element + "-alert", padding, var(--space-12) var(--space-20) 0);

    @include mixins.from-tablet {
      @include mixins.use-logical($element + "-tablet-alert", padding, 0);
    }
  }

  &--has-error {
    --_list-item-background: #{functions.var($element, background, transparent)};
    --list-item-background: var(--_list-item-background);
    --list-item-hover-background: var(--_list-item-background);

    @include mixins.from-tablet {
      --_list-item-background: #{functions.var($element + "-tablet", background)};
    }

    #{$this}__option {
      --_list-item-option-background: #{functions.var($element + "-option", background)};
      --list-item-background: var(--_list-item-option-background);
      --list-item-hover-background: var(--_list-item-option-background);
    }
  }
}
</style>
