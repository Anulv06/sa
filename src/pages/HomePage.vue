<script setup lang="ts">
import { ref } from 'vue'

const ip = ref('')
const mask = ref('255.255.255.255')
const options = ["255.255.255.255", "255.255.255.254", "255.255.255.252", "255.255.255.248", "255.255.255.240", "255.255.255.224", "255.255.255.192", "255.255.255.128", "255.255.254.0", "255.255.252.0", "255.255.248.0", "255.255.240.0", "255.255.224.0", "255.255.192.0", "255.255.128.0", "255.254.0.0", "255.252.0.0", "255.248.0.0", "255.240.0.0", "255.224.0.0", "255.192.0.0", "255.128.0.0", "254.0.0.0", "252.0.0.0", "248.0.0.0", "240.0.0.0", "224.0.0.0", "192.0.0.0", "128.0.0.0", "0.0.0.0"]
const isShowResult = ref(false)

function isIpValid(ip: string): boolean {
  return (
    /^(\d{1,3})\.(\d{1,3})\.(\d{1,3})\.(\d{1,3})$/.test(ip) &&
    ip.split('.').every((octet) => Number(octet) <= 255)
  );
}

function showResult() {
  isShowResult.value = true
}

function getAddressesCount(mask: string): number {
  let binaryMask = '';

  for (const octet of mask.split('.')) {
    binaryMask += Number(octet).toString(2).padStart(8, '0');
  }
  const zeros = 32 - binaryMask.replaceAll('0', '').length;

  if (zeros === 0) return 1;
  if (zeros === 1) return 2;

  return Math.pow(2, zeros) - 2;
}
</script>

<template>
  <div class="ip-calculator">
    <form @submit.prevent="showResult" class="ip-calculator__form">
      <input
        v-model="ip"
        type="text"
        placeholder="Введите IP-адрес"
        class="ip-calculator__input"
      />
      <select v-model="mask" class="ip-calculator__select">
        <option
          v-for="option in options"
          :key="option"
          :value="option"
        >
          {{ option }}
        </option>
      </select>
      <button
        type="submit"
        :disabled="!isIpValid(ip)"
        class="ip-calculator__button"
      >
        Рассчитать
      </button>
    </form>

    <div v-if="isShowResult && isIpValid(ip)" class="ip-calculator__result">
      <div class="ip-calculator__result-item">
        <span class="ip-calculator__label">IP:</span>
        <span class="ip-calculator__value">{{ ip }}</span>
      </div>
      <div class="ip-calculator__result-item">
        <span class="ip-calculator__label">Маска подсети:</span>
        <span class="ip-calculator__value">{{ mask }}</span>
      </div>
      <div class="ip-calculator__result-item">
        <span class="ip-calculator__label">Количество адресов:</span>
        <span class="ip-calculator__value">{{ getAddressesCount(mask) }}</span>
      </div>
    </div>
  </div>
</template>