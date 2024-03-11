<template>
  <div :class="`input-container ${id}-container`">
    <label :for="id">{{ label }}</label>
    <input
      type="text"
      :id="id"
      :class="isFilled !== undefined && (isFilled ? 'success' : 'error')"
      v-model="input"
      @input="filterInput"
      @blur="handleBlur"
      :minlength="minlength"
      :maxlength="dynamicMaxLength"
      required
    />
  </div>
</template>

<script>
export default {
  name: 'CardInput',
  props: {
    label: String,
    id: String,
    type: String,
    minlength: Number,
    maxlength: Number,
    spacing: Number
  },
  data() {
    return {
      input: '',
      cleanedInputValue: '',
      modifiedInputValue: '',
      dynamicMaxLength: 0,
      isFilled: undefined,
      status: false
    };
  },
  emits: ['input-value', 'input-status'],
  created() {
    if (this.spacing) {
      this.dynamicMaxLength = this.maxlength + Math.floor(this.maxlength / this.spacing);
      if (this.maxlength % this.spacing === 0) {
        this.dynamicMaxLength -= 1;
      }
    } else {
      this.dynamicMaxLength = this.maxlength;
    }
  },
  methods: {
    // Method to apply character spacing
    charSpacing(val) {
      if(this.type === "number"){
        if(this.spacing){
          let formattedInput = val.match(new RegExp(`.{1,${this.spacing}}`, 'g'));
          return formattedInput ? formattedInput.join(' ') : '';
        }
        return val;
      }
      return val;
    },
    // Method to append 'X' to numbers if length is less than maxlength
    xCoding(val) {
      if (this.type === "number") {
        let result = '';

        val.length < this.maxlength ?
          (result = val + 'X'.repeat(parseInt(this.maxlength - val.length))) :
          (result = val.slice(0, this.maxlength));

        return result;
      }
      return val;
    },
    // Method to filter input based on type and emit modified value
    filterInput(event) {
      // Remove non-numeric characters from the input
      this.type === "number" &&
        (this.input = event.target.value.replace(/[^\d]/g, ''))
      // Remove non-alphabetic characters from the input
      this.type === "string" &&
        (this.input = event.target.value.replace(/[^a-zA-Z\s]/g, ''))

      this.cleanedInputValue = this.input;
      this.modifiedInputValue = this.charSpacing(this.xCoding(this.cleanedInputValue));
      this.input = this.charSpacing(this.input);
      // Update status of component
      this.minlength > this.cleanedInputValue.length ?
        (this.status = false) :
        (this.status = true);
      // Emit the modifiedInputValue to the parent component
      this.$emit('input-value', this.modifiedInputValue.toUpperCase(), this.id, this.status);
    },
    // Method to handle blur event and update input filled status
    handleBlur() {this.isFilled = this.status}
  }
}
</script>
