<template>


    <div v-if="n.metadataExternal.name.includes('Solsunsets SE') || n.metadataExternal.symbol == 'SUNS'"
         class="my-5 nes-container with-title text-xs">
        <h2 class="title">
            {{ n.metadataExternal.name }} | <a v-bind:href="'https://solscan.io/token/' + n.mint" target="_blank">Solscan</a>&nbsp;


        </h2>
        <div v-if="n.metadataExternal.name.includes('Solsunsets SE') || n.metadataExternal.symbol == 'SUNS'">
            <div class="flex flex-row"> <img :alt="n.mint" :src="n.metadataExternal.properties.files[1].uri" /> </div>
        </div>

    </div>

    <div v-if="n.metadataExternal.name.includes('Solsunsets #') || n.metadataExternal.symbol == 'SUNS'"
         class="my-5 nes-container with-title text-xs">
        <h2 class="title">
            {{ n.metadataExternal.name }} | <a v-bind:href="'https://solscan.io/token/' + n.mint" target="_blank">Solscan</a>&nbsp;


        </h2>
      
            <div id="ssuns" class="sp">

                <div class="flex flex-row">
                    <img :alt="n.mint" :src="n.metadataExternal.properties.files[1].uri" />
                </div>
                <div class="flex flex-row">
                </div>
            </div>
            <div id="ssuns" class="og">
                <div class="flex flex-row">
                    <img :alt="n.mint" :src="n.metadataExternal.properties.files[3].uri" />
                </div>
                <div class="pfpog flex flex-row">
                    <img :alt="n.mint" :src="n.metadataExternal.properties.files[2].uri" />
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