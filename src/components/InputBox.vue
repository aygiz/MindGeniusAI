<script setup lang="ts">
import { computed, ref, watch } from 'vue'
import { Icon } from '@iconify/vue'

const props = defineProps({
  isLoading: {
    type: Boolean,
    default: false,
  },
  message: {
    type: String,
    default: '',
  },
})
const emit = defineEmits(['sendMessage', 'update:message'])

const newMessage = ref(props.message)
const inputWrapper = ref<HTMLElement | null>(null)

const isLoadingRef = computed(() => props.isLoading)
const isCanClick = computed(() => newMessage.value.trim() !== '')

watch(() => props.message, (val) => {
  newMessage.value = val
})

watch(() => newMessage.value, (val) => {
  emit('update:message', val)
})

function sendMessage() {
  if (newMessage.value.trim() !== '') {
    emit('sendMessage', newMessage.value.trim())
    newMessage.value = ''
  }
}

function handleEnter(e: any) {
  if (e.key === 'Enter' && e.altKey) {
    newMessage.value = `${newMessage.value}\n`
  }
  else if (e.key === 'Enter' && !e.altKey) {
    e.preventDefault()
    sendMessage()
  }
}
</script>

<template>
  <div ref="inputWrapper" class="flex items-center px-4 py-2 rounded-md relative">
    <div class="flex-1 mr-2">
      <a-spin tip="wait a minute..." :spinning="isLoadingRef">
        <a-textarea
          v-model:value="newMessage" type="text" class="w-full px-4 py-2 border border-gray-300 rounded-md"
          placeholder="Type your message..." auto-size @keydown="handleEnter"
        />
      </a-spin>
    </div>
    <a-button type="primary" :disabled="!isCanClick" @click="sendMessage">
      <template #icon>
        <span class="button-icon mr-1">
          <Icon icon="bi:send-fill" width="14" />
        </span>
      </template>
      Send
    </a-button>
  </div>
</template>

<style scoped>

</style>
