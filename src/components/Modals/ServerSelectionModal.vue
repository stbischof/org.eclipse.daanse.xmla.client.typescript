<!-- eslint-disable @typescript-eslint/no-unused-vars -->
<script lang="ts">
import { usePromisifiedModal } from "@/composables/promisifiedModal";
import { ref } from "vue";

export default {
  name: "ServerSelectionModal",
  setup() {
    const serverUrl = ref("");
    const reset = () => {
      serverUrl.value = "";
    };

    const { isOpened, run, close } = usePromisifiedModal(reset);

    return {
      serverUrl,
      isOpened,
      run,
      close,
      reset,
    };
  },
  methods: {
    ok() {
      if (!this.serverUrl) return;
      this.close(this.serverUrl);
    },
  },
};
</script>
<template>
  <va-modal :modelValue="isOpened" no-padding class="server-url-modal" @ok="ok">
    <template #content="{ ok }">
      <va-card-title class="va-h6">Enter server url:</va-card-title>
      <va-card-content>
        <va-input
          class="mb-2 server-url-input"
          v-model="serverUrl"
          placeholder="Server url"
        />
      </va-card-content>
      <va-card-actions>
        <va-button @click="ok" color="warning">Ok!</va-button>
      </va-card-actions>
    </template>
  </va-modal>
</template>
<style lang="scss">
.server-url-modal {
  .va-modal__container {
    width: 100%;
  }

  .server-url-input {
    width: 100%;
  }

  .va-modal__dialog {
    margin: auto;
  }
}
</style>
