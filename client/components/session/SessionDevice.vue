<template>
  <div class="bg-white dark:bg-gray-800 rounded-lg shadow relative">
    <div class="absolute right-0 top-0 m-2">
      <push-button size="xs" @click="confirm(session)">
        <icon-client
          icon="mdi-trash"
          class="w-4 h-4 text-red-400"
        />
      </push-button>
    </div>
    <div class="flex items-center justify-center py-4 border-gray-200 border-b dark:border-gray-600">
      <div :class="`device device-${type}`" />
    </div>
    <div class="p-4">
      <div>
        <div class="flex items-center justify-between text-gray-900 dark:text-gray-300 mb-2">
          {{ name }}
          <icon-client
            v-if="session.current"
            icon="mdi-check-decagram"
            class="w-4 h-4 mr-1.5 text-green-400"
          />
        </div>
        <div class="flex items-center text-gray-500 text-sm mb-1">
          <icon-client
            icon="mdi-application-outline"
            class="w-4 h-4 mr-1.5 text-gray-400"
          />
          {{ session.device.browser ? session.device.browser : source }}
        </div>
        <div class="flex items-center text-gray-500 text-sm mb-1">
          <icon-client
            icon="mdi-map"
            class="w-4 h-4 mr-1.5 text-gray-400"
          />
          {{ session.location.city }}, {{ session.location.state }}, {{ session.location.country }}
        </div>
        <div
          v-if="session.source === 'google'"
          class="flex items-center text-gray-500 text-sm mb-1"
        >
          <icon-client
            icon="flat-color-icons:google"
            class="w-4 h-4 mr-1.5"
          />
          Verified through Google
        </div>
        <div
          v-if="session.source === 'github'"
          class="flex items-center text-gray-500 text-sm mb-1"
        >
          <icon-client icon="fa-brands:github" class="w-4 h-4 mr-1.5" />
          Verified through Github
        </div>
        <div
          v-if="session.source === 'email'"
          class="flex items-center text-gray-500 text-sm mb-1"
        >
          <icon-client icon="mdi-email" class="w-4 h-4 mr-1.5" />
          Verified through E-mail
        </div>
        <div class="flex items-center text-gray-500 text-sm mb-1">
          <icon-client
            icon="mdi-clock"
            class="w-4 h-4 mr-1.5 text-gray-400"
          />
          Created {{ $dayjs(session.created_at).fromNow() }}
        </div>
        <div class="flex items-center text-gray-500 text-sm mb-1">
          <icon-client
            icon="mdi-clock"
            class="w-4 h-4 mr-1.5 text-gray-400"
          />
          Last activity {{ $dayjs(session.updated_at).fromNow() }}
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { PushButton } from 'tailvue'
import IconClient from '~/components/IconClient.vue'
import { PropType } from '@vue/runtime-core'
import { useNuxtApp } from '#app'
import { computed } from '@vue/reactivity'
const { $toast, $modal, $api } = useNuxtApp()
const props = defineProps({
  session: {
    type: Object as PropType<api.Session>,
    required: true,
  },
})
const emit = defineEmits(['refresh'])

const type = computed((): string => {
  if (props.session.device.platform.includes('macOS')) return 'mac'
  if (props.session.device.platform.includes('OS X')) return 'mac'
  if (props.session.device.platform.includes('Windows')) return 'windows'
  if (props.session.device.platform.includes('Linux')) return 'linux'
  // if (this.session.device.platform.includes('iOS')) return 'ios'
  if (props.session.agent.includes('iPhone')) return 'iphone'
  if (props.session.agent.includes('iPad')) return 'ipad'
  if (props.session.agent.includes('Android')) return 'android'
  if (props.session.agent.includes('Chrome')) return 'chrome'
  if (props.session.agent.includes('Safari')) return 'chrome'
  if (props.session.agent.includes('Edge')) return 'edge'
  return 'other'
})
const source = computed((): string => {
    if (props.session.source === 'actions') return 'Github Actions'
    if (props.session.source === 'pipelines') return 'Bitbucket Pipelines'
    if (props.session.source === 'cli') return 'Fume CLI'
    if (props.session.source === 'ci') return 'CI / CD'
    return 'Unknown'
})
const name = computed((): string => {
  if (props.session.device.name) return props.session.device.name
  if (props.session.device.platform) return props.session.device.platform
  return props.session.agent
})

function confirm (session: api.Session) {
  $modal.show({
    type: 'danger',
    title: 'Delete Session',
    body: 'Are you sure you want to delete this session ?',
    primary: {
      label: 'Delete Session',
      theme: 'red',
      action: () => revoke(session),
    },
    secondary: {
      label: 'Cancel',
      theme: 'white',
      action: () => $toast.info('Delete cancelled'),
    },
  })
}

async function revoke (session: api.Session) {
  if (session.current) return $api.logout()
  $toast.show((await $api.delete(`/session/${session.token}`)).data)
  emit('refresh')
}

</script>
