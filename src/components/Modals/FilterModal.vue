<!-- eslint-disable @typescript-eslint/no-unused-vars -->
<script lang="ts">
import { usePromisifiedModal } from "@/composables/promisifiedModal";
import { ref } from "vue";
import FilterTreeView from "../Filters/FilterTreeView.vue";

export default {
  name: "ServerSelectionModal",
  emits: ["setFilters"],
  setup() {
    const filterConfigured = ref({});
    const rootHierarchy = ref({});
    const reset = () => {
      filterConfigured.value = {};
    };
    const multipleChoise = ref(false);
    const currentlySelected = ref(null as any);
    const opened = ({ element }: { element: any }) => {
      rootHierarchy.value = {
        item: element.originalItem,
        filters: element.filters,
      };

      const initialFilters = element.filters;
      multipleChoise.value = initialFilters.multipleChoise;

      if (initialFilters.multipleChoise) {
        filterConfigured.value = {
          enabled: initialFilters.enabled,
          multipleChoise: initialFilters.multipleChoise,
          selectAll: initialFilters.selectAll,
          selectedItems: initialFilters.selectedItems,
          deselectedItems: initialFilters.deselectedItems,
        };
      } else {
        filterConfigured.value = {
          enabled: initialFilters.enabled,
          multipleChoise: initialFilters.multipleChoise,
          selectedItem: initialFilters.selectedItem,
        };

        currentlySelected.value = initialFilters.selectedItem;
      }
    };
    const { isOpened, run, close } = usePromisifiedModal(reset, opened);

    const setSelection = ({
      enabled,
      multipleChoise,
      selectedItem,
      selectAll,
      selectedItems,
      deselectedItems,
    }: {
      enabled: boolean;
      multipleChoise: boolean;
      selectAll: boolean;
      selectedItem: any;
      selectedItems: any[];
      deselectedItems: any[];
    }) => {
      if (multipleChoise) {
        filterConfigured.value = {
          enabled,
          multipleChoise,
          selectAll,
          selectedItems,
          deselectedItems,
        };
      } else {
        filterConfigured.value = {
          enabled,
          multipleChoise,
          selectedItem,
        };

        currentlySelected.value = selectedItem;
      }
    };

    return {
      filterConfigured,
      isOpened,
      run,
      close,
      rootHierarchy,
      setSelection,
      multipleChoise,
      currentlySelected,
    };
  },
  methods: {
    ok() {
      this.close({ filters: this.filterConfigured });
    },
    cancel() {
      this.close({});
    },
    resetSelection() {
      this.$refs.filterTreeView?.resetSelection();
    },
  },
  components: { FilterTreeView },
};
</script>
<template>
  <va-modal
    :modelValue="isOpened"
    no-padding
    class="filter-modal"
    @ok="ok"
    fixed-layout
  >
    <template #content="{ ok }">
      <va-card-title class="va-h6">Enable any filters:</va-card-title>
      <va-card-content>
        <Suspense>
          <FilterTreeView
            ref="filterTreeView"
            :rootHierarchy="rootHierarchy"
            @set-selection="setSelection"
          ></FilterTreeView>
        </Suspense>
      </va-card-content>
      <va-card-actions class="actions">
        <div class="action-buttons">
          <va-button @click="ok" color="primary">Confirm</va-button>
          <va-button @click="cancel" color="secondary">Cancel</va-button>
        </div>
        <div
          v-if="!multipleChoise && currentlySelected && currentlySelected.id"
        >
          <div>Currently selected: {{ currentlySelected.Caption }}</div>
          <div class="reset-button" @click="resetSelection">
            Reset selection
          </div>
        </div>
      </va-card-actions>
    </template>
  </va-modal>
</template>
<style lang="scss">
.filter-modal {
  .va-modal--fixed-layout .va-modal__inner {
    height: calc(100vh - 2rem);
  }

  .va-modal__container {
    width: 100%;
  }

  .va-modal__dialog {
    margin: auto;
  }

  .va-modal__inner > div {
    height: 100%;
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }

  .va-card__content {
    overflow: hidden;
    display: flex;
    width: 100%;
    height: 100%;
  }

  .va-card__content > div {
    flex-direction: column;
    overflow: hidden;
    width: 100%;
  }

  .actions {
    display: flex;
    justify-content: space-between !important;
  }

  .reset-button {
    margin-top: 0.25rem;
    color: var(--va-primary);
    text-decoration: underline;
    user-select: none;
    cursor: pointer;
  }
}
</style>
