<template>
  <main>
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-auto">
          <form class="row justify-content-center">
            <div class="col-auto">
              <input type="url" placeholder="Enter URL link" v-model="urlToGenerate" class="form-control url-input">
            </div>
            <div class="col-auto">
              <button class="btn btn-primary" @click.prevent="generateQRCode(urlToGenerate)">Generate</button>
            </div>
          </form>
          <div class="row justify-content-center">
            <div class="col-auto my-3">
              <div class="qr-code" ref="qrCodeComponent"></div>
            </div>
          </div>
          <template v-if="isQRCodeGenerated">
            <div class="row justify-content-center">
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
            </div>
          </template>
        </div>
      </div>
    </div>
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

function generateQRCode(url: string) {
  qrCode.update({ data: url });
  qrCode.append(qrCodeComponent.value);
  isQRCodeGenerated.value = true;
}

function download(name: string, extension: FileExtension) {
  qrCode.download({ name: name, extension: extension });
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
