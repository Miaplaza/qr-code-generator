<template>
    <form ref="generateQrCodeForm" class="row justify-content-center" novalidate>
      <div class="col-auto has-validation url-input">
        <input ref="urlInput" v-model="urlData" type="url" placeholder="Enter URL link" :minlength="$props.minUrlLength"
          :maxlength="$props.maxUrlLength" required :readonly="isQrCodeGenerated" class="form-control">
        <div class="invalid-feedback">
          {{ urlError }}
        </div>
      </div>
      <div class="col-auto">
        <button class="btn btn-primary" :disabled="isQrCodeGenerated" @click.prevent="generateQrCode(urlData)">
          Generate
        </button>
      </div>
    </form>
  </template>
  <script setup lang="ts">
  import { ref } from 'vue';
  
  const props = defineProps<{
    minUrlLength: number,
    maxUrlLength: number
  }>();
  
  const emit = defineEmits<{
    generateQrCode: [url: string],
  }>()
  
  defineExpose({
    reset
  });
  
  const generateQrCodeForm = ref<HTMLFormElement>(null!);
  const urlInput = ref<HTMLInputElement>(null!);
  
  const isQrCodeGenerated = ref(false);
  const urlData = ref("");
  const urlError = ref("");
  
  function generateQrCode(url: string): void {
    if (!validateQrCodeGenerationForm()) {
      return;
    }
  
    emit('generateQrCode', url);
    isQrCodeGenerated.value = true;
  }
  
  function reset(): void {
    urlData.value = "";
    isQrCodeGenerated.value = false;
    generateQrCodeForm.value.classList.remove('was-validated');
    urlInput.value.removeEventListener('input', validateUrlInput);
  }
  
  function validateQrCodeGenerationForm(): boolean {
    validateUrlInput();
  
    if (urlError.value !== "") {
      // Forces the form to show validation result.
      generateQrCodeForm.value.classList.add('was-validated');
      urlInput.value.addEventListener('input', validateUrlInput);
    }
  
    return urlError.value === "";
  }
  
  function validateUrlInput(): void {
    urlError.value = getUrlInputValidationError();
    // Changes the validation state of the URL input.
    // The message itself doesn't matter (if empty, the input is valid), but the state is dependent fron 'urlError' variable.
    urlInput.value.setCustomValidity(urlError.value);
  }
  
  function getUrlInputValidationError(): string {
    // Checked by 'required' attribute.
    if (urlInput.value.validity.valueMissing) {
      return "Please enter a URL link.";
      // Checked by 'type="url"' attribute.
    } else if (urlInput.value.validity.typeMismatch) {
      return "Please enter a correct URL link.";
      // Checked by 'minlength' attribute.
    } else if (urlInput.value.validity.tooShort) {
      return `The URL link should have at least ${props.minUrlLength} symbols in order to create a scannable QR code.
        Your link has ${urlData.value.length} symbols.`;
    } else {
      return "";
    }
  }
  </script>
  <style scoped lang="scss">
  .url-input {
    width: 400px;
  }
  </style>
  