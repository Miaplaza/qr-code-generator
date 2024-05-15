<template>
  <main class="container">
    <div class="row justify-content-center my-4">
      <div class="col-auto">
        <h1>QR code generator</h1>
      </div>
    </div>
    <QrCodeValidationForm ref="qrCodeValidationForm" :min-url-length="minUrlLength" :max-url-length="maxUrlLength"
      @generate-qr-code="onGenerateQrCode" />
    <div class="row justify-content-center my-3">
      <div class="col-auto">
        <div ref="qrCodeComponent" class="qr-code"></div>
      </div>
    </div>
    <template v-if="isQrCodeGenerated">
      <QrCodeDownloadForm @download-qr-code="onDownloadQrCode"/>
      <div class="row justify-content-center mt-5">
        <div class="col-auto">
          <button class="btn btn-lg btn-success" :disabled="!isQrCodeGenerated" @click.prevent="reset()">
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
import QrCodeValidationForm from './QrCodeValidationForm.vue';
import QrCodeDownloadForm from './QrCodeDownloadForm.vue';

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
// URL with length less than minUrlLength is non-scannable.
const minUrlLength = 12;
const maxUrlLength = urlLinkLengthHardLimitMap[errorCorrectionLevel];

const qrCodeComponent = ref<HTMLElement>(null!);
const qrCodeValidationForm = ref<InstanceType<typeof QrCodeValidationForm> | null>(null);

const isQrCodeGenerated = ref(false);
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

function onGenerateQrCode(url: string): void {
  qrCode.update({ data: url });
  qrCode.append(qrCodeComponent.value);
  isQrCodeGenerated.value = true;
}

function reset(): void {
  isQrCodeGenerated.value = false;
  // Clears the QR code.
  qrCodeComponent.value.replaceChildren();
  qrCodeValidationForm.value?.reset();
}

async function onDownloadQrCode(extension: FileExtension): Promise<void> {
  await qrCode.download({ name: 'Aki_QR_code', extension: extension });
}
</script>
<style scoped lang="scss">

.qr-code {
  border: 1px solid black;
}
</style>
