<template>
  <div class="form-group"
       :class="[
       {'input-group': hasIcon},
       {'has-danger': error && dirty},
       {'input-group-focus': focused},
       {'has-label': label || $slots.label},
       {'has-success': hasSuccess}]">
    <slot name="label">
      <label v-if="label" :class="labelClasses">
        {{label}}
        <span v-if="required">*</span>
      </label>
    </slot>
    <div v-if="addonLeftIcon || $slots.addonLeft" class="input-group-prepend">
        <span class="input-group-text">
          <slot name="addonLeft">
            <i :class="addonLeftIcon"></i>
          </slot>
        </span>
    </div>
    <slot>
      <input
        :value="value"
        v-on="listeners"
        v-bind="$attrs"
        class="form-control "
        :class="[{valid: value && !error}, inputClasses, error ? 'form-control-danger' : '']"
        aria-describedby="addon-right addon-left">
    </slot>
    <div v-if="addonRightIcon || $slots.addonRight" class="input-group-append">
          <span class="input-group-text">
              <slot name="addonRight">
                  <i :class="addonRightIcon"></i>
              </slot>
          </span>
    </div>
    <slot name="infoBlock"></slot>
    <slot name="helpBlock">
      <collapse-transition :duration="300">
        <div class="text-danger invalid-feedback aba__form--error" style="display: block;" :class="{'mt-2': hasIcon}" v-if="error && dirty ">
          {{ error }}
        </div>
      </collapse-transition>
    </slot>
  </div>
</template>
<script>
  import CollapseTransition from "vue2-transitions/src/Collapse/CollapseTransition";

  export default {
    components: {CollapseTransition},
    inheritAttrs: false,
    name: 'fg-input',
    props: {
      required: {
        type: Boolean,
        description: 'Whether input is required (adds an asterix *)'
      },
      label: {
        type: String,
        description: 'Input label (text before input)'
      },
      error: {
        type: String,
        description: 'Input error (below input)'
      },
      labelClasses: {
        type: String,
        description: 'Input label css classes'
      },
      inputClasses: {
        type: String,
        description: 'Input css classes'
      },
      value: {
        type: [String, Number],
        description: 'Input value'
      },
      addonRightIcon: {
        type: String,
        description: 'Addon right icon'
      },
      addonLeftIcon: {
        type: String,
        description: 'Addont left icon'
      }
    },
    data() {
      return {
        touched: false,
        focused: false,
        hadError: false,
        dirty: false
      }
    },
    computed: {
      listeners() {
        return {
          ...this.$listeners,
          input: this.updateValue,
          focus: this.onFocus,
          blur: this.onBlur
        }
      },
      hasSuccess() {
        return this.hadError && this.touched && !this.error
      },
      hasIcon() {
        const {addonRight, addonLeft} = this.$slots
        return addonRight !== undefined || addonLeft !== undefined || this.addonRightIcon !== undefined || this.addonLeftIcon !== undefined
      }
    },
    methods: {
      updateValue(evt) {
        let value = evt.target.value
        if (!this.touched && value) {
          this.touched = true
        }
        this.$emit('input', value)
      },
      onFocus(value) {
        this.focused = true;
        this.dirty = true;

        this.$emit('focus', value);
      },
      onBlur(value) {
        this.focused = false;
        this.$emit('blur', value);
      }
    },
    created() {
      this.$watch('error', (newVal) => {
        if (newVal) {
          this.hadError = true;
        }
      }, {immediate: true})
    }
  }
</script>
<style lang="scss">
  .input-group-text {
    padding: 2px !important;
  }

</style>
