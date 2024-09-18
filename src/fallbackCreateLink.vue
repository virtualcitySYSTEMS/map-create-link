<script setup lang="ts">
  import { VSheet } from 'vuetify/components';
  import {
    VcsTextField,
    VcsButton,
    setStateToUrl,
    NotificationType,
    VcsUiApp,
  } from '@vcmap/ui';
  import { ComponentPublicInstance, inject, ref } from 'vue';

  const props = defineProps({
    link: {
      type: String,
      required: true,
    },
  });
  const localLink = ref(props.link);
  const app = inject<VcsUiApp>('vcsApp')!;
  const input = ref<ComponentPublicInstance>();

  function copy(): void {
    const element: HTMLInputElement | undefined = (
      input.value?.$el as HTMLElement
    ).querySelector('input') as HTMLInputElement | undefined;
    if (element) {
      element.select();
      document.execCommand('copy');
      app.notifier.add({
        title: 'createLink.title',
        message: 'createLink.copied',
        type: NotificationType.SUCCESS,
      });
    }
  }

  const refresh = async (): Promise<void> => {
    const state = await app.getState(true);
    const url = new URL(window.location.href);
    setStateToUrl(state, url);
    localLink.value = url.toString();
  };
</script>
<template>
  <v-sheet class="d-flex flex-row align-center pl-2 pr-2">
    <VcsTextField :value="localLink" ref="input" />
    <div class="d-flex col-2 justify-end pa-0">
      <VcsButton
        icon="mdi-content-copy"
        class="mr-2"
        @click="copy"
        :title="$t('createLink.copyToClipboard')"
      />
      <VcsButton
        icon="mdi-refresh"
        class="mr-2"
        @click="refresh"
        :title="$t('createLink.refreshTooltip')"
      />
    </div>
  </v-sheet>
</template>

<style scoped></style>
