<template>
  <div>
    <!--all the config stuff-->
    <ConfigPane />
    <NFTViewForm :is-loading="isLoading" @submit-form="handleSubmitForm">
     
    </NFTViewForm>

    <!--per NFT display-->
    <LoadingBar v-if="isLoading" :progress="progress" :text="text" class="my-5" />
    <NotifyError v-else-if="isError" class="mt-5">{{ text }}</NotifyError>
    <div v-else>
      <NFTViewCard v-for="n in NFTs" :key="n.mint" :n="n"></NFTViewCard>
    </div>

    <!--modals-->
   

    <!--must sit at the very bottom-->
    <infinite-loading
      @infinite="infiniteHandler"
      :identifier="
        +new Date() //needs to be something thta dynamically updates, or won't work
      "
      spinner="spiral"
    ></infinite-loading>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, ref, watch } from 'vue';
import InfiniteLoading from 'vue-infinite-loading';
import 'vue-json-pretty/lib/styles.css';
import ConfigPane from '@/components/ConfigPane.vue';
import LoadingBar from '@/components/LoadingBar.vue';
import { NFTGet } from '@/common/NFTget';
import NFTViewCard from '@/components/NFTViewCard.vue';
import useLoading, { LoadStatus } from '@/composables/loading';
import { EE, ERR_NO_NFTS } from '@/globals';
import { INFT, INFTParams } from '@/common/helpers/types';
import NFTViewForm from '@/components/NFTViewForm.vue';
import useDownload from '@/composables/download';
import useCopy from '@/composables/copy';
import NotifyError from '@/components/notifications/NotifyError.vue';
import QuestionMark from '@/components/QuestionMark.vue';
import ModalWindow from '@/components/ModalWindow.vue';
import useModal from '@/composables/modal';

export default defineComponent({
  components: {

    NotifyError,
    NFTViewForm,
    NFTViewCard,
    LoadingBar,
    ConfigPane,
    InfiniteLoading,
  },
  setup() {
    const {
      progress,
      text,
      isLoading,
      isError,
      updateLoading,
      updateLoadingStdErr,
      updateLoadingStdWin,
    } = useLoading();
    const displayedNFTs = ref<INFT[]>([]); // this is what's shown on FE
    const allFetchedNFTs = ref<INFT[]>([]); // this is everything fetched in mem
    const fetchParams = ref<INFTParams | null>(null);
    const NFTCount = computed(() => displayedNFTs.value.length + allFetchedNFTs.value.length);

    const getNextBatch = (size: number): INFT[] => {
      if (allFetchedNFTs.value.length === 0) {
        return [];
      }
      if (allFetchedNFTs.value.length > size) {
        return allFetchedNFTs.value.splice(0, size);
      }
      return allFetchedNFTs.value.splice(0, allFetchedNFTs.value.length);
    };

    const fetchNFTs = (params: INFTParams) => {
      updateLoading({
        newStatus: LoadStatus.Loading,
        newProgress: 0,
        maxProgress: 50,
        newText: 'Looking for NFTs... ETA: <1 min',
      });

      EE.removeAllListeners();
      EE.on('loading', updateLoading);

      // clear for new fetch
      displayedNFTs.value = [];
      allFetchedNFTs.value = [];

      NFTGet(params)
        .then((fetchedNFTs) => {
          if (fetchedNFTs.length) {
            allFetchedNFTs.value = fetchedNFTs;
            const nextBatch = getNextBatch(10);
            displayedNFTs.value.push(...nextBatch);
            updateLoadingStdWin();
          } else {
            updateLoadingStdErr(ERR_NO_NFTS);
          }
        })
        .catch(updateLoadingStdErr);
    };

    const handleSubmitForm = (params: INFTParams) => {
      fetchParams.value = params;
      fetchNFTs(params);
    };

    // --------------------------------------- display
    const infiniteHandler = ($state: any) => {
      const nextBatch = getNextBatch(10);
      if (nextBatch.length) {
        displayedNFTs.value.push(...nextBatch);
        $state.loaded();
      } else {
        $state.complete();
      }
    };

    // --------------------------------------- export
 

    // --------------------------------------- sharing
   

    // --------------------------------------- modal

  },
});
</script>

<style>
/*temp hackaround...*/
.infinite-status-prompt {
  @apply text-black !important;
}
</style>
