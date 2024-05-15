<template>
  <main class="container">
    <div class="row justify-content-center my-4">
      <div class="col-auto">
        <h1>QR code generator</h1>
      </div>
    </div>
    <form ref="generateQRCodeForm" class="row justify-content-center" novalidate>
      <div class="col-auto has-validation url-input">
        <input ref="urlInput" v-model="urlToGenerate" type="url" placeholder="Enter URL link" minlength="25" required
          :readonly="isQRCodeGenerated" class="form-control">
        <div class="invalid-feedback">
          Please enter a correct URL link.
          <br>
          Also, make sure your URL contains at least 25 symbols and no more than 280 symbols
          in order to create a scannable QR code.
          You can add whitespaces at the end if the link is too short.
        </div>
      </div>
      <div class="col-auto">
        <button class="btn btn-primary" :disabled="isQRCodeGenerated" @click.prevent="generateQrCode(urlToGenerate)">
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
          <button class="btn btn-primary" @click.prevent="download('qrCode', extension)">
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
  type FileExtension,
  type Options
} from 'qr-code-styling';

const blackColor = "#000000";
const whiteColor = "#ffffff";
const maxUrlLength = 280;

const generateQRCodeForm = ref<HTMLFormElement>(null!);
const qrCodeComponent = ref<HTMLElement>(null!);
const urlInput = ref<HTMLInputElement>(null!);

const isQRCodeGenerated = ref(false);
const urlToGenerate = ref("");
const extension = ref<FileExtension>('png');
const options = ref<Options>({
  width: 300,
  height: 300,
  type: 'svg',
  image: '/qr-code-generator/aki.png',
  margin: 10,
  qrOptions: {
    typeNumber: 0,
    mode: 'Byte',
    errorCorrectionLevel: 'H'
  },
  imageOptions: {
    hideBackgroundDots: true,
    imageSize: 0.7,
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

function generateQrCode(url: string): void {
  if (!validateQrCodeGenerationForm()) {
    return;
  }

  qrCode.update({ data: url });
  qrCode.append(qrCodeComponent.value);
  isQRCodeGenerated.value = true;
}

function validateQrCodeGenerationForm(): boolean {
  urlInput.value.setCustomValidity('');

  if (!generateQRCodeForm.value.checkValidity()) {
    generateQRCodeForm.value.classList.add('was-validated');
    return false;
  }
  // Makes max length limitation soft, without cutoff (unlike 'maxlength' attribute).
  if (urlInput.value.value.length > maxUrlLength) {
    // Triggers invalid state of the URL input.
    // The error message doesn't matter because we will show predefined error message.
    urlInput.value.setCustomValidity('error');
    generateQRCodeForm.value.classList.add('was-validated');
    return false;
  }

  return true;
}

function reset(): void {
  isQRCodeGenerated.value = false;
  urlToGenerate.value = "";
  generateQRCodeForm.value.classList.remove('was-validated');
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
