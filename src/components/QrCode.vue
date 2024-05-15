<template>
  <main class="container">
    <div class="row justify-content-center my-4">
      <div class="col-auto">
        <h1>QR code generator</h1>
      </div>
    </div>
    <form ref="qrCodeForm" class="row justify-content-center" novalidate :inert="isQRCodeGenerated">
      <div class="col-auto has-validation url-input">
        <input type="url" placeholder="Enter URL link" v-model="urlToGenerate" class="form-control" required>
        <div class="invalid-feedback">
          Please enter a correct URL link.
        </div>
      </div>
      <div class="col-auto">
        <button class="btn btn-primary" @click.prevent="generateQRCode(urlToGenerate)"
          :disabled="isQRCodeGenerated">
          Generate
        </button>
      </div>
    </form>
    <div class="row justify-content-center my-3">
      <div class="col-auto">
        <div class="qr-code" ref="qrCodeComponent"></div>
      </div>
    </div>
    <template v-if="isQRCodeGenerated">
      <form class="row justify-content-center">
        <div class="col-auto">
          <select class="form-select" v-model="extension">
            <option value="svg">SVG</option>
            <option selected value="png">PNG</option>
            <option value="jpeg">JPEG</option>
            <option value="webp">WEBP</option>
          </select>
        </div>
        <div class="col-auto">
          <button class="btn btn-primary" @click.prevent="download('qrCode', extension)">Download</button>
        </div>
      </form>
      <div class="row justify-content-center mt-5">
        <div class="col-auto">
          <button class="btn btn-lg btn-success" @click.prevent="reset()" :disabled="!isQRCodeGenerated">Generate
            new QR code</button>
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

const isQRCodeGenerated = ref(false);
const urlToGenerate = ref("");

const qrCodeForm = ref<HTMLFormElement>(null!);
const qrCodeComponent = ref<HTMLElement>(null!);
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

function generateQRCode(url: string): void {
  if (!qrCodeForm.value.checkValidity()) {
    isQRCodeGenerated.value = false;
    qrCodeForm.value.classList.add('was-validated');
    qrCodeComponent.value.replaceChildren();
    return;
  }

  qrCode.update({ data: url });
  qrCode.append(qrCodeComponent.value);
  isQRCodeGenerated.value = true;
}

function reset(): void {
  isQRCodeGenerated.value = false;
  qrCodeForm.value.classList.remove('was-validated');
  qrCodeComponent.value.replaceChildren();
  urlToGenerate.value = "";
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
