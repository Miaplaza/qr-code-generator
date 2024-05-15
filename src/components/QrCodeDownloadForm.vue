<template>
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
        <button class="btn btn-primary" @click.prevent="$emit('downloadQrCode', extension)">
          Download
        </button>
      </div>
    </form>
  </template>
  <script setup lang="ts">
  import { ref } from 'vue';
  import {
    type FileExtension
  } from 'qr-code-styling';
  
  defineEmits<{
    downloadQrCode: [extension: FileExtension],
  }>()
  
  const extension = ref<FileExtension>('png');
  </script>
  
  <style scoped lang="scss"></style>
  