<template lang="pug">
  q-chat-message(:name="name"
    :avatar="avatar"
    :text="message.data"
    :stamp="timestamp"
    :sent="message.sent")
</template>

<script lang="ts">
import { ServerConnection, OutputMessage } from 'src/chat/types'
import {
  defineComponent,
  computed,
  PropType,
  ref,
  toRefs
} from '@vue/composition-api'

import moment from 'moment'

export default defineComponent({
  name: 'Message',
  props: {
    message: {
      type: Object as PropType<OutputMessage>,
      required: true
    },
    server: {
      type: Object as PropType<ServerConnection>,
      required: true
    }
  },
  setup(props, { root }) {
    const { server, message } = toRefs(props)
    const avatar = computed(() =>
      message.value.sent
        ? root.$store.getters['local/avatar']
        : server.value.avatar
    )
    const now = ref(Date.now())
    setInterval(() => (now.value = Date.now()), 3000)
    const timestamp = computed(() =>
      moment(message.value.timestamp).from(now.value)
    )
    const name = computed(() =>
      message.value.sent ? root.$tc('me') : server.value.name
    )
    return { timestamp, avatar, name }
  }
})
</script>
