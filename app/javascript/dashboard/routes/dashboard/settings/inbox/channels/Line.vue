<script>
import { mapGetters } from 'vuex';
import { useVuelidate } from '@vuelidate/core';
import { useAlert } from 'dashboard/composables';
import { required } from '@vuelidate/validators';
import router from '../../../../index';
import PageHeader from '../../SettingsSubPageHeader.vue';
import NextButton from 'dashboard/components-next/button/Button.vue';

export default {
  components: {
    PageHeader,
    NextButton,
  },
  setup() {
    return { v$: useVuelidate() };
  },
  data() {
    return {
      channelName: '',
      lineChannelId: '',
      lineChannelSecret: '',
      lineChannelToken: '',
    };
  },
  computed: {
    ...mapGetters({
      uiFlags: 'inboxes/getUIFlags',
    }),
  },
  validations: {
    channelName: { required },
    lineChannelId: { required },
    lineChannelSecret: { required },
    lineChannelToken: { required },
  },
  methods: {
    async createChannel() {
      this.v$.$touch();
      if (this.v$.$invalid) {
        return;
      }

      try {
        const lineChannel = await this.$store.dispatch(
          'inboxes/createChannel',
          {
            name: this.channelName,
            channel: {
              type: 'line',
              line_channel_id: this.lineChannelId,
              line_channel_secret: this.lineChannelSecret,
              line_channel_token: this.lineChannelToken,
            },
          }
        );

        router.replace({
          name: 'settings_inboxes_add_agents',
          params: {
            page: 'new',
            inbox_id: lineChannel.id,
          },
        });
      } catch (error) {
        useAlert(this.$t('INBOX_MGMT.ADD.LINE_CHANNEL.API.ERROR_MESSAGE'));
      }
    },
  },
};
</script>

<template>
  <div
    class="border border-n-weak bg-n-solid-1 rounded-t-lg border-b-0 h-full w-full p-6 col-span-6 overflow-auto"
  >
    <PageHeader
      :header-title="$t('INBOX_MGMT.ADD.LINE_CHANNEL.TITLE')"
      :header-content="$t('INBOX_MGMT.ADD.LINE_CHANNEL.DESC')"
    />
    <form
      class="flex flex-wrap flex-col mx-0"
      @submit.prevent="createChannel()"
    >
      <div class="flex-shrink-0 flex-grow-0">
        <label :class="{ error: v$.channelName.$error }">
          {{ $t('INBOX_MGMT.ADD.LINE_CHANNEL.CHANNEL_NAME.LABEL') }}
          <input
            v-model="channelName"
            type="text"
            :placeholder="
              $t('INBOX_MGMT.ADD.LINE_CHANNEL.CHANNEL_NAME.PLACEHOLDER')
            "
            @blur="v$.channelName.$touch"
          />
          <span v-if="v$.channelName.$error" class="message">{{
            $t('INBOX_MGMT.ADD.LINE_CHANNEL.CHANNEL_NAME.ERROR')
          }}</span>
        </label>
      </div>

      <div class="flex-shrink-0 flex-grow-0">
        <label :class="{ error: v$.lineChannelId.$error }">
          {{ $t('INBOX_MGMT.ADD.LINE_CHANNEL.LINE_CHANNEL_ID.LABEL') }}
          <input
            v-model="lineChannelId"
            type="text"
            :placeholder="
              $t('INBOX_MGMT.ADD.LINE_CHANNEL.LINE_CHANNEL_ID.PLACEHOLDER')
            "
            @blur="v$.lineChannelId.$touch"
          />
        </label>
      </div>

      <div class="flex-shrink-0 flex-grow-0">
        <label :class="{ error: v$.lineChannelSecret.$error }">
          {{ $t('INBOX_MGMT.ADD.LINE_CHANNEL.LINE_CHANNEL_SECRET.LABEL') }}
          <input
            v-model="lineChannelSecret"
            type="text"
            :placeholder="
              $t('INBOX_MGMT.ADD.LINE_CHANNEL.LINE_CHANNEL_SECRET.PLACEHOLDER')
            "
            @blur="v$.lineChannelSecret.$touch"
          />
        </label>
      </div>

      <div class="flex-shrink-0 flex-grow-0">
        <label :class="{ error: v$.lineChannelToken.$error }">
          {{ $t('INBOX_MGMT.ADD.LINE_CHANNEL.LINE_CHANNEL_TOKEN.LABEL') }}
          <input
            v-model="lineChannelToken"
            type="text"
            :placeholder="
              $t('INBOX_MGMT.ADD.LINE_CHANNEL.LINE_CHANNEL_TOKEN.PLACEHOLDER')
            "
            @blur="v$.lineChannelToken.$touch"
          />
        </label>
      </div>

      <div class="w-full mt-4">
        <NextButton
          :is-loading="uiFlags.isCreating"
          type="submit"
          solid
          blue
          :label="$t('INBOX_MGMT.ADD.LINE_CHANNEL.SUBMIT_BUTTON')"
        />
      </div>
    </form>
  </div>
</template>
