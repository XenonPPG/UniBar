<script setup lang="ts">
import SafeIcon from "@/components/SafeIcon.vue";
import {ref, watch} from "vue";
import Button from "@/components/Button.vue";

const CHARACTERS = ['𝄀', '𝄁', '𝄂', '𝄃']
const MAX_LENGTH = 25;

const length = ref(10);
const includeSpaces = ref(true);
const barcode = ref(generateBarcode());
const notification = ref(false);
let notificationTimeout: any = null;

function generateBarcode() {
  let barcode = '';
  const charset = includeSpaces.value ? [' ', ...CHARACTERS] : CHARACTERS;

  for (let i = 0; i < length.value; i++) {
    const currentCharset = (i === 0 || i === length.value - 1)
        ? CHARACTERS
        : charset;

    barcode += currentCharset[Math.floor(Math.random() * currentCharset.length)];
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
watch(includeSpaces, () => {
  barcode.value = generateBarcode();
})
</script>

<template>
  <div class="flex size-full min-h-screen">
    <div class="p-3 md:p-5 flex flex-1 flex-col justify-between overflow-x-hidden">
      <header class="w-min group">
        <h1 class="text-4xl md:text-5xl group-hover:animate-bounce">UniBar</h1>
      </header>

      <div class="size-full flex-center flex-col gap-5 py-6">
        <p class="text-secondary">length: {{ length }}</p>

        <div class="flex w-full justify-center px-2 min-h-35"
             :style="{ fontSize: `min(150px, calc(220vw / ${length}))` }">
          <p v-for="(ch, index) in [...barcode]"
             :key="index"
             class="border-b-2 md:border-b-4 border-secondary select-none leading-none flex items-end"
             :style="{
               fontFamily: 'serif',
               margin: '0 0.02em',
               width: ch === ' ' ? '0.25em' : 'auto'
             }">
            {{ ch }}
          </p>
        </div>

        <div class="flex flex-col gap-4">
          <div class="flex-center flex-wrap gap-2">
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
            <Button class="flex-center gap-2 text-xl md:text-2xl w-max px-4 py-2" @click="copyToClipboard">
              <SafeIcon icon="radix-icons:copy" class="size-6 md:size-8 text-xl md:text-2xl"/>
              <span>Copy</span>
            </Button>
          </div>

          <div class="flex items-center gap-1">
            <input
                v-model="includeSpaces"
                type="checkbox"
                id="include-spaces"
                class="size-5 appearance-none rounded bg-bg border border-secondary checked:bg-secondary relative checked:after:content-[''] checked:after:absolute checked:after:left-1.5 checked:after:top-0.5 checked:after:w-1.5 checked:after:h-2.75 checked:after:border-r-2 checked:after:border-b-2 checked:after:border-white checked:after:rotate-45">
            <label for="include-spaces" class="text-lg md:text-xl select-none">Include spaces</label>
          </div>
        </div>
      </div>

      <footer class="flex flex-col md:flex-row items-center md:justify-between gap-6 md:gap-2">
        <a href="https://github.com/XenonPPG/UniBar" target="_blank"
           class="flex-center gap-2 size-min text-secondary select-none hover:cursor-pointer hover:text-primary transition-colors">
          <SafeIcon icon="radix-icons:github-logo" class="size-6 md:size-7"/>
          <span class="text-lg md:text-xl">GitHub</span>
        </a>

        <div class="flex flex-col md:flex-row text-secondary text-xs md:text-sm gap-2 text-center md:text-left">
          <p>[WARNING]</p>

          <div class="flex flex-col gap-1 md:gap-0">
            <div v-for="phrase in [
                'Produced barcodes are not real and cannot be scanned',
                'Unicode doesn\'t provide enough characters for real barcodes'
            ]" class="flex flex-wrap md:flex-nowrap gap-x-1 md:gap-x-2 justify-center md:justify-between w-full">
              <span v-for="word in phrase.split(' ')">
                {{ word }}
              </span>
            </div>
          </div>
        </div>
      </footer>
    </div>
  </div>

  <div class="fixed top-20 left-1/2 -translate-x-1/2 select-none opacity-0 transition-opacity duration-500"
       :class="{'opacity-100':notification}">
    <p class="text-lg md:text-xl p-2 px-5 rounded-[100px] border-3 border-secondary bg-secondary/20">Copied</p>
  </div>
</template>