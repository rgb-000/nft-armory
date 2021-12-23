<template>
     <form @submit.prevent="emitSubmitForm" :class="chosenMethod === 'creator' ? 'mt-5' : 'mt-10'">
         <button class="nes-btn is-primary"
                 :class="{ 'is-disabled': isLoading }"
                 :disabled="isLoading"
                 type="submit">
             Load files &nbsp;<b>&#707;</b>
         </button>
        <slot />
    </form>
    <NotifyError v-if="error" class="mt-5"> Uh oh something went wrong - {{ error }}</NotifyError>
  
</template>

<script lang="ts">
import { computed, defineComponent, onMounted, ref, watch } from 'vue';
import 'vue-json-pretty/lib/styles.css';
import { useRoute } from 'vue-router';
import { INFTParams } from '@/common/helpers/types';
import useWallet from '@/composables/wallet';
import QuestionMark from '@/components/QuestionMark.vue';
import ModalWindow from '@/components/ModalWindow.vue';
import ContentTooltipCreator from '@/components/content/tooltip/ContentTooltipCreator.vue';
import useModal from '@/composables/modal';
import useError from '@/composables/error';
import NotifyError from '@/components/notifications/NotifyError.vue';
import { DEFAULTS } from '@/globals';
import NotifyInfo from '@/components/notifications/NotifyInfo.vue';

export default defineComponent({
  components: { NotifyInfo, NotifyError, ContentTooltipCreator, ModalWindow, QuestionMark },
  props: {
    isLoading: Boolean,
  },
  emits: ['submit-form'],
  setup(props, ctx) {
    const { error, clearError, tryConvertToPk } = useError();

    // --------------------------------------- params
    const owner = ref<string | null>(null);
    const creator = ref<string | null>(null);
    const authority = ref<string | null>(null);
    const mint = ref<string | null>(null);

    // --------------------------------------- choosing a method
    const chosenMethod = ref<string>('address');
    const byAddress = computed(() => chosenMethod.value === 'address');
    const byWallet = computed(() => chosenMethod.value === 'wallet');
    const byCreator = computed(() => chosenMethod.value === 'creator');
    const byAuthority = computed(() => chosenMethod.value === 'authority');
    const byMint = computed(() => chosenMethod.value === 'mint');

    // --------------------------------------- wallet
    const { wallet, getWalletAddress } = useWallet();
    watch(byWallet, () => {
      if (getWalletAddress()) owner.value = getWalletAddress()!.toBase58();
    });
    watch(wallet, () => {
      if (getWalletAddress()) owner.value = getWalletAddress()!.toBase58();
    });
    const missingWallet = computed(() => getWalletAddress() === null);
    const missingWalletNotice = 'Please connect your wallet above.';

    const prepareParams = (): INFTParams | null => {
      clearError();
      const params = {
        owner: byAddress.value || byWallet.value ? tryConvertToPk(owner.value) : undefined,
        creator: byCreator.value ? tryConvertToPk(creator.value) : undefined,
        updateAuthority: byAuthority.value ? tryConvertToPk(authority.value) : undefined,
        mint: byMint.value ? tryConvertToPk(mint.value) : undefined,
      } as INFTParams;
      if (error.value) {
        return null;
      }
      return params;
    };

    const emitSubmitForm = () => {
      const params = prepareParams();
      if (params) ctx.emit('submit-form', params);
    };

    // --------------------------------------- modal
    const { registerModal, isModalVisible, showModal, hideModal } = useModal();
    registerModal('tooltipCreator');

    // --------------------------------------- sharing links
    onMounted(() => {
      const route = useRoute();
      const {
        address: goAddress,
        creator: goCreator,
        authority: goAuthority,
        mint: goMint,
      } = route.params;

      if (goAddress) {
        chosenMethod.value = 'address';
        owner.value = goAddress as any as string;
        emitSubmitForm();
      } else if (goCreator) {
        chosenMethod.value = 'creator';
        creator.value = goCreator as any as string;
        emitSubmitForm();
      } else if (goAuthority) {
        chosenMethod.value = 'authority';
        authority.value = goAuthority as any as string;
        emitSubmitForm();
      } else if (goMint) {
        chosenMethod.value = 'mint';
        mint.value = goMint as any as string;
        emitSubmitForm();
      }
    });

    return {
      DEFAULTS,
      error,
      // params
      owner,
      creator,
      authority,
      mint,
      // method
      chosenMethod,
      byAddress,
      byWallet,
      byCreator,
      byAuthority,
      byMint,
      // wallet
      missingWallet,
      missingWalletNotice,
      // event
      emitSubmitForm,
      // modal
      isModalVisible,
      showModal,
      hideModal,
    };
  },
});
</script>

<style scoped></style>
