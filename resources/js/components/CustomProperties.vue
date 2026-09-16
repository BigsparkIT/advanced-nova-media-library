<template>
  <transition name="fade" v-if="showModal">
    <CustomPropertiesModal
      :fields="filledFields"
      @close="handleClose"
      @update="handleUpdate"
    />
  </transition>
  <div>
    <div v-for="field in filledFields" :key="field.attribute">
      {{field.name}}: {{labelFor(field)}}
    </div>
  </div>
</template>

<script>
import CustomPropertiesModal from './CustomPropertiesModal'
import tap from 'lodash/tap'
import get from 'lodash/get'
import set from 'lodash/set'
import clone from 'lodash/clone'

export default {
  props: {
    modelValue: {
      type: Object,
      required: true,
    },
    fields: {
      type: Array,
      required: true,
    },
    showModal: {
      type: Boolean,
      required: true,
    },
  },

  components: {
    CustomPropertiesModal,
  },

  computed: {
    filledFields() {
      return JSON.parse(JSON.stringify(this.fields)).map(field => tap(field, field => {
        field.value = this.getProperty(field.attribute)
      }))
    }
  },

  methods: {
    handleClose() {
      this.$emit('close')
    },

    handleUpdate(formData) {
      for (let [property, value] of formData.entries()) {
        this.setProperty(property, value)
      }

      this.$emit('update:modelValue', this.modelValue)

      this.handleClose()
    },

    getProperty(property) {
      return get(this.modelValue, `custom_properties.${property}`)
    },

    setProperty(property, value) {
      set(this.modelValue, `custom_properties.${property}`, value)
    },
  }
}
</script>

