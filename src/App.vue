<script setup lang="ts">
import SafeIcon from "@/components/SafeIcon.vue";
import {ref, watch} from "vue";
import Button from "@/components/Button.vue";

const CHARACTERS = ['𝄀', '𝄁', '𝄂', '𝄃']
const MAX_LENGTH = 25;

const length = ref(10);
const barcode = ref(generateBarcode());
const notification = ref(false);
let notificationTimeout: any = null;

function generateBarcode() {
  let barcode = '';
  for (let i = 0; i < length.value; i++) {
    barcode += CHARACTERS[Math.floor(Math.random() * CHARACTERS.length)];
  }
  return barcode;
}

function copyToClipboard() {
  notification.value = true;
  navigator.clipboard.writeText(barcode.value);
  if (notificationTimeout) {
    clearTimeout(notificationTimeout);
  }
  notificationTimeout = setTimeout(() => {
    notification.value = false;
  }, 1500);
}

watch(length, () => {
  barcode.value = generateBarcode();
})
</script>

<template>
  <div class="flex size-full min-h-screen">
    <div class="p-5 flex flex-1 flex-col justify-between">
      <header class="w-min group">
        <h1 class="text-5xl group-hover:animate-bounce">UniBar</h1>
      </header>

      <div class="size-full flex-center flex-col gap-5">
        <p class="text-secondary">length: {{ length }}</p>
        <div class="flex">
          <p v-for="ch in [...barcode]" class="text-[150px] mx-0.5 border-b-4 border-secondary"
             style="font-family: serif">{{ ch }}</p>
        </div>

        <div class="flex-center gap-2">
          <Button variant="secondary" @click="length--" :disabled="length === 1">
            <SafeIcon icon="radix-icons:minus" class="size-8 text-2xl"/>
          </Button>
          <Button variant="secondary" @click="length++" :disabled="length === MAX_LENGTH">
            <SafeIcon icon="radix-icons:plus" class="size-8 text-2xl"/>
          </Button>
          <Button variant="secondary" class="group" @click="barcode = generateBarcode()">
            <SafeIcon icon="radix-icons:reload"
                      class="size-8 text-2xl group-hover:rotate-90 transition-transform duration-500"/>
          </Button>
          <Button class="flex-center gap-2 text-2xl w-max" @click="copyToClipboard">
            <SafeIcon icon="radix-icons:copy" class="size-8 text-2xl"/>
            <span>Copy</span>
          </Button>
        </div>
      </div>

      <footer class="flex-center justify-between">
        <a href="https://github.com/XenonPPG/UniBar" target="_blank" class="flex-center gap-2 size-min text-secondary select-none hover:cursor-pointer hover:text-primary transition-colors">
          <SafeIcon icon="radix-icons:github-logo" class="size-7"/>
          <span class="text-xl">GitHub</span>
        </a>

        <div class="flex text-secondary text-sm gap-2">
          <p>[WARNING]</p>

          <div class="flex flex-col">
            <div v-for="phrase in [
                'Produced barcodes are not real and cannot be scanned',
                'Unicode doesn\'t provide enough characters for real barcodes'
            ]" class="flex gap-2 justify-between w-full">
              <span v-for="word in phrase.split(' ')">
                {{ word }}
              </span>
            </div>
          </div>
        </div>
      </footer>
    </div>
  </div>

  <!-- notification -->
  <div class="fixed top-5 left-1/2 -translate-x-1/2 select-none opacity-0 transition-opacity duration-500"
       :class="{'opacity-100':notification}">
    <p class="text-xl p-2 px-5 rounded-[100px] border-3 border-secondary bg-secondary/20">Copied</p>
  </div>
</template>