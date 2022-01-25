<script setup lang="ts">
import { PropType, ref } from 'vue'
import Popper from 'vue3-popper'
import CountryType from '../types/CountryType'

const emit = defineEmits([
  'countrySelected'
])

const props = defineProps({
  values: {
    required: true,
    type: Object as PropType<CountryType[]>
  },
  error: {
    type: Boolean
  }
})

const labelContent = ref<string>('Country')
const popperOpen = ref<boolean>(false)
const isFilled = ref<boolean>(false)

function isSelected(country: string) {
  isFilled.value = true
  labelContent.value = country
  emit('countrySelected', country)
}
</script>

<template>
  <div class="select" :class="{ open: popperOpen, error: error }">
    <img src="../assets/arrow.svg" />
    <Popper
      @open:popper="popperOpen = true"
      @close:popper="popperOpen = false"
      offsetSkid="140"
      offsetDistance="24"
    >
      <button class="label" :class="{ filled: isFilled }">{{ labelContent }}</button>
      <template #content="{ close }">
        <div class="items" @click="close">
          <div
            v-for="(country, index) in props.values"
            :key="index"
            @click="isSelected(country.name)"
          >{{ country.name }}</div>
        </div>
      </template>
    </Popper>
  </div>
</template>

<style lang="scss" scoped>
:deep(.popper) {
  padding: 18px;
  box-shadow: 0px 20px 34px -12px rgba(0, 0, 0, 0.16);
  background-color: #ffffff;
  color: #76879e;
  font-size: 16px;
  line-height: 21px;
  width: 100%;
  position: relative;
  border-radius: 14px;
  max-height: 300px;
  overflow: auto;
}
.select {
  position: relative;
  width: 100%;
  border-radius: 14px;
  border: 1px solid transparent;
  font-family: inherit;
  padding: 12px 18px 0 18px;
  height: 64px;
  font-size: 16px;
  background: #f6f8fa;
  color: #1b2c45;
  transition: all 0.15s ease;
  &:hover {
    border: 1px solid #d5dbe2;
  }
  &.error {
    border: 1px solid #f43c3c;
  }
  &.open {
    background-color: #ffffff;
    box-shadow: 0px 12px 34px -12px rgba(0, 0, 0, 0.16);
    border: 1px solid transparent;
    border-bottom-left-radius: 0;
    border-bottom-right-radius: 0;
    img {
      transform: rotate(180deg);
    }
  }
  img {
    position: absolute;
    right: 20px;
    top: 50%;
    transform: translateY(-50%);
    transition: all 0.15s ease;
  }
  .label {
    position: absolute;
    top: 20px;
    left: 0;
    text-align: left;
    width: 100%;
    height: 100%;
    font-size: 16px;
    color: #76879e;
    font-size: 16px;
    transform-origin: 0 0;
    transform: translate3d(7px, -20px, 0);
    transition: all 0.2s ease;
    border: none;
    z-index: 1;
    background-color: transparent;
  }
  .items {
    div {
      padding: 6px 0;
      cursor: pointer;
    }
  }
}
</style>

