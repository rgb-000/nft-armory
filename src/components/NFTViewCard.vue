<template>

    <div v-if="n.metadataExternal.symbol === 'SUNSH'" class="my-5 nes-container with-title text-xs">
        <h2 class="title">
            {{ n.metadataExternal.name }} | <a v-bind:href="'https://gallery.solsunsets.com/view/mint/' + n.mint" target="_blank">Share &#129133;</a>&nbsp;<a v-bind:href="'https://solscan.io/token/' + n.mint" target="_blank">Explorer &#129133;</a>&nbsp;<a v-bind:href="'https://moonrank.app/collection/solsunsets/' + n.mint" target="_blank">Moonrank &#129133;</a>

        </h2>
        <div class="flex flex-row">
            <img :alt="n.mint" :src="n.metadataExternal.image" />
            <img :alt="n.mint" :src="n.metadataExternal.properties.files[1].uri" />

            <div class="ml-5 text-gray-400">
            </div>
        </div>

    </div>
    <div v-if="n.metadataExternal.symbol === 'SSUNS'" class="my-5 nes-container with-title text-xs">
        <h2 class="title">
            {{ n.metadataExternal.name }} | <a v-bind:href="'https://gallery.solsunsets.com/view/mint/' + n.mint" target="_blank">Share &#129133;</a>&nbsp;<a v-bind:href="'https://solscan.io/token/' + n.mint" target="_blank">Explorer &#129133;</a>&nbsp;<a v-bind:href="'https://moonrank.app/collection/solsunsets/' + n.mint" target="_blank">Moonrank &#129133;</a>

        </h2>
        <div class="flex flex-row">
            <img :alt="n.mint" :src="n.metadataExternal.image" />
            <img :alt="n.mint" :src="n.metadataExternal.properties.files[1].uri" />

            <div class="ml-5 text-gray-400">
            </div>
        </div>

    </div>
    <div v-if="n.metadataExternal.symbol === 'SUNS'" class="my-5 nes-container with-title text-xs">
        <h2 class="title">
            {{ n.metadataExternal.name }} | <a v-bind:href="'https://gallery.solsunsets.com/view/mint/' + n.mint" target="_blank">Share &#129133;</a>&nbsp;<a v-bind:href="'https://solscan.io/token/' + n.mint" target="_blank">Explorer &#129133;</a>&nbsp;<a v-bind:href="'https://moonrank.app/collection/solsunsets/' + n.mint" target="_blank">Moonrank &#129133;</a>

        </h2>
        <div class="flex flex-row">
            <img :alt="n.mint" :src="n.metadataExternal.image" />
            <img :alt="n.mint" :src="n.metadataExternal.properties.files[1].uri" />

            <div class="ml-5 text-gray-400">
            </div>
        </div>

    </div>
</template>

<script lang="ts">
    import { defineComponent, ref } from 'vue';
    import VueJsonPretty from 'vue-json-pretty';
    import { stringifyPubkeysAndBNsInObject } from '@/common/helpers/util';
    import useModal from '@/composables/modal';
    import ModalWindow from '@/components/ModalWindow.vue';
    import ContentTooltipJSON from '@/components/content/tooltip/ContentTooltipJSON.vue';
    import QuestionMark from '@/components/QuestionMark.vue';
    import useCopy from '@/composables/copy';
    import ContentTooltipRarity from '@/components/content/tooltip/ContentTooltipRarity.vue';

    export default defineComponent({
        props: {
            n: Object,
        },
        components: {
            ContentTooltipRarity,
            QuestionMark,
            ContentTooltipJSON,
            ModalWindow,
            VueJsonPretty,
        },
        setup() {
            const isMaster = (editionType: string) => editionType.toLowerCase().includes('master');
            const fullJSON = ref(false);
            const NOT_FOUND = 'Not found';
            const toggleJSON = () => {
                fullJSON.value = !fullJSON.value;
            };
            // --------------------------------------- clipboard
            const { copyText, doCopyJSON } = useCopy();
            // --------------------------------------- modal
            const { registerModal, isModalVisible, showModal, hideModal } = useModal();
            registerModal('tooltipJSON');
            registerModal('tooltipRarity');
            return {
                isMaster,
                fullJSON,
                toggleJSON,
                stringifyPubkeysAndBNsInObject,
                // clipboard
                copyText,
                doCopyJSON,
                // modal
                isModalVisible,
                showModal,
                hideModal,
                NOT_FOUND,
            };
        },
    });
</script>

<style scoped>
    img {
        max-height: 200px;
        max-width: 200px;
    }

    p {
        @apply my-2;
    }

    .copy-father {
        position: relative;
    }

    .copy {
        position: absolute;
        top: 0;
        right: 0;
        z-index: 100;
    }
</style>