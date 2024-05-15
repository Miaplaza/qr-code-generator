<template>
  <main class="container">
    <div class="row justify-content-center my-4">
      <div class="col-auto">
        <h1>QR code generator</h1>
      </div>
    </div>
    <form ref="generateQRCodeForm" class="row justify-content-center" novalidate>
      <div class="col-auto has-validation url-input">
        <input ref="urlInput" v-model="urlToGenerate" type="url" placeholder="Enter URL link" :minlength="minUrlLength"
          required :readonly="isQRCodeGenerated" class="form-control">
        <div class="invalid-feedback">
          {{ urlError }}
        </div>
      </div>
      <div class="col-auto">
        <button class="btn btn-primary" :disabled="isQRCodeGenerated" @click.prevent="generateQRCode(urlToGenerate)">
          Generate
        </button>
      </div>
    </form>
    <div class="row justify-content-center my-3">
      <div class="col-auto">
        <div ref="qrCodeComponent" class="qr-code"></div>
      </div>
    </div>
    <template v-if="isQRCodeGenerated">
      <form class="row justify-content-center">
        <div class="col-auto">
          <select v-model="extension" class="form-select">
            <option value="svg">SVG</option>
            <option selected value="png">PNG</option>
            <option value="jpeg">JPEG</option>
            <option value="webp">WEBP</option>
          </select>
        </div>
        <div class="col-auto">
          <button class="btn btn-primary" @click.prevent="download('Aki_QR_code', extension)">
            Download
          </button>
        </div>
      </form>
      <div class="row justify-content-center mt-5">
        <div class="col-auto">
          <button class="btn btn-lg btn-success" :disabled="!isQRCodeGenerated" @click.prevent="reset()">
            Generate new QR code
          </button>
        </div>
      </div>
    </template>
  </main>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import QRCodeStyling, {
  type ErrorCorrectionLevel,
  type FileExtension,
  type Options
} from 'qr-code-styling';

const blackColor = "#000000";
const whiteColor = "#ffffff";
const errorCorrectionLevel: ErrorCorrectionLevel = "Q";

// Limits of the QR code version 40 (the biggest one).
const urlLinkLengthHardLimitMap: Record<ErrorCorrectionLevel, number> = {
  L: 2953,
  M: 2331,
  Q: 1663,
  H: 1273
}

// These are experementally figured out values which generate scannable QR codes accounting 
// current parameters (error correction level, image size, margins).
// Values outside of this range generate unscannable QR codes.
const minUrlLength = 12;
const maxUrlLength = 482;

// Additionally limits max length of data by biggest possible QR code version.
var limitedMaxUrlLength = Math.min(maxUrlLength, urlLinkLengthHardLimitMap[errorCorrectionLevel]);

const generateQRCodeForm = ref<HTMLFormElement>(null!);
const qrCodeComponent = ref<HTMLElement>(null!);
const urlInput = ref<HTMLInputElement>(null!);

const isQRCodeGenerated = ref(false);
const urlToGenerate = ref("");
const urlError = ref("");
const extension = ref<FileExtension>('png');
const options = ref<Options>({
  width: 300,
  height: 300,
  type: 'svg',
  image: '/qr-code-generator/aki.png',
  margin: 5,
  qrOptions: {
    typeNumber: 0,
    mode: 'Byte',
    errorCorrectionLevel: errorCorrectionLevel
  },
  imageOptions: {
    hideBackgroundDots: true,
    imageSize: 0.5,
    margin: 5,
    crossOrigin: 'anonymous',
  },
  dotsOptions: {
    type: 'square',
    color: blackColor
  },
  backgroundOptions: {
    color: whiteColor,
  },
  cornersSquareOptions: {
    type: 'square',
    color: blackColor
  },
  cornersDotOptions: {
    type: 'square',
    color: blackColor
  }
});
var qrCode = new QRCodeStyling(options.value);

function generateQRCode(url: string): void {
  if (!validateQRCodeGenerationForm()) {
    return;
  }

  qrCode.update({ data: url });
  qrCode.append(qrCodeComponent.value);
  isQRCodeGenerated.value = true;
}

function validateQRCodeGenerationForm(): boolean {
  validateUrlInput();

  if (urlError.value !== "") {
    // Forces the form to show validation result.
    generateQRCodeForm.value.classList.add('was-validated');
    urlInput.value.addEventListener('input', validateUrlInput);
  }

  return urlError.value === "";
}

function getUrlInputValidationError(): string {
  if (urlInput.value.validity.valueMissing) {
    return "Please enter a URL link.";
  } else if (urlInput.value.validity.typeMismatch) {
    return "Please enter a correct URL link.";
  } else if (urlInput.value.validity.tooShort) {
    return `The URL link should have at least ${minUrlLength} symbols in order to create a scannable QR code.
      Your link has ${urlToGenerate.value.length} symbols.`;
  // Makes max length limitation soft, without cutoff (unlike 'maxlength' attribute).
  } else if (urlToGenerate.value.length > limitedMaxUrlLength) {
    return `The URL link should have no more than ${limitedMaxUrlLength} symbols in order to create a scannable QR code.
      Your link has ${urlToGenerate.value.length} symbols.`
  } else {
    return "";
  }
}

function validateUrlInput(): void {
  urlError.value = getUrlInputValidationError();
  urlInput.value.setCustomValidity(urlError.value);
}

function reset(): void {
  isQRCodeGenerated.value = false;
  urlToGenerate.value = "";
  generateQRCodeForm.value.classList.remove('was-validated');
  urlInput.value.removeEventListener('input', validateUrlInput);
  // Clears the QR code.
  qrCodeComponent.value.replaceChildren();
}

async function download(name: string, extension: FileExtension): Promise<void> {
  await qrCode.download({ name: name, extension: extension });
}
</script>

<style scoped lang="scss">
.url-input {
  width: 400px;
}

.qr-code {
  border: 1px solid black;
}
</style>
