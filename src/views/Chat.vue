<template>
  <v-container>
    <v-row>
      <v-col class="pb-0" cols="12" sm="8">
        <v-text-field
          v-model="userName"
          label="User Name"
          outlined
        ></v-text-field
      ></v-col>
      <v-col class="pb-0" cols="6" sm="2">
        <v-select
          :items="languages"
          label="Original Language"
          outlined
          v-model="fromLang"
        ></v-select>
      </v-col>
      <v-col class="pb-0" cols="6" sm="2">
        <v-select
          :items="languages"
          label="Translate to"
          outlined
          v-model="toLang"
        ></v-select>
      </v-col>
      <v-col class="pt-0" cols="12">
        <v-text-field
          v-model="message"
          label="Message"
          outlined
          append-outer-icon="mdi-send"
          @click:append-outer="sendMessage"
        ></v-text-field></v-col
    ></v-row>
    <Timeline :messages="computedSortedMessages"></Timeline>
  </v-container>
</template>

<script>
import { API, graphqlOperation, Predictions } from 'aws-amplify'
import { listMessages } from '@/graphql/queries'
import { createMessage } from '@/graphql/mutations'
import { onCreateMessage } from '@/graphql/subscriptions'

import Timeline from '../components/Timeline'

export default {
  components: { Timeline },
  computed: {
    computedSortedMessages: function () {
      return this.messages
        .slice()
        .sort((a, b) => (a.createdAt < b.createdAt ? 1 : -1))
    },
  },
  data: function () {
    return {
      userName: '',
      message: '',
      fromLang: '',
      toLang: '',
      messages: [],
      languages: ['en', 'ja'],
      onCreateMessageSubscription: null,
    }
  },
  created: async function () {
    const result = await API.graphql(graphqlOperation(listMessages))
    console.log(result)
    this.messages = result.data.listMessages.items

    this.unsubscribe = API.graphql(graphqlOperation(onCreateMessage)).subscribe(
      {
        next: ({ provider, value }) => {
          console.log({ provider, value })
          this.messages.push(value.data.onCreateMessage)
        },
      },
    )
  },
  beforeDestroy: function () {
    if (this.onCreateMessageSubscription) {
      this.onCreateMessageSubscription.unsubscribe()
      this.onCreateMessageSubscription = null
    }
  },
  methods: {
    sendMessage: async function () {
      console.log('sendMessage', this.message)
      const result = await Predictions.convert({
        translateText: {
          source: {
            text: this.message,
            language: this.fromLang,
            // supported languages https://docs.aws.amazon.com/translate/latest/dg/how-it-works.html#how-it-works-language-codes
          },
          targetLanguage: this.toLang,
        },
      })
      console.log(result)

      const message = await API.graphql(
        graphqlOperation(createMessage, {
          input: {
            userName: this.userName,
            originalMessage: this.message,
            translatedMessage: result.text,
          },
        }),
      )
      console.log(message)

      this.userName = ''
      this.message = ''
      this.fromLang = ''
      this.toLang = ''
    },
  },
}
</script>

<style></style>
