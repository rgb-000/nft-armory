<template>
    <div>

        <div class="sfooter desk">

            Solsunsets&copy; 2022 | Powered by <a alt="home" href="https://solsunsets.com">
                <object href="https://solsunsets.com" id="svg1" data="https://solsunsets.com/img/logo/logo-sln.svg" class="logo-sln"></object>
            </a>
        </div>


        <div class="blog">

            <div class="logo">
                <a href="https://solsunsets.com">
                    <object href="https://solsunsets.com" id="svg1" data="https://solsunsets.com/img/logo/logo-ss-m.svg" class="logo-ss"></object>
                </a>
            </div>
            <div class="blog-header-container">
                <div class="blog-intro desk">
                    <div class="logo-links">

                        <a alt="home" href="https://solsunsets.com">
                            <object href="https://solsunsets.com" id="svg1" data="https://solsunsets.com/img/logo/home.svg" class="logo-twitter mob"></object>
                        </a>
                        <a href="https://twitter.com/solsunsets">
                            <object href="https://twitter.com/solsunsets" id="svg1" data="https://solsunsets.com/img/logo/logo-ttr.svg" class="logo-twitter"></object>
                        </a><a href="https://discord.gg/RNSRHaxzbR">
                            <object href="https://discord.gg/RNSRHaxzbR" id="svg1" data="https://solsunsets.com/img/logo/logo-dsc.svg" class="logo-discord"></object>
                        </a><a href="https://docs.solsunsets.com">
                            <object href="https://docs.solsunsets.com" id="svg1" data="https://solsunsets.com/img/logo/logo-gtb.svg" class="logo-discord"></object>
                        </a>
                        <a href="https://www.magiceden.io/marketplace/solsunsets">
                            <object href="https://www.magiceden.io/marketplace/solsunsets" id="svg1" data="https://solsunsets.com/img/logo/logo-me.svg" class="logo-discord"></object>
                        </a>


                    </div>
                    <span>Solsunsets is an Art Collection of 4,444 generative landscapes running on the Solana chain inspired by the aesthetics of popular culture, nostalgic childhood memories, obsolete technologies and jazz fusion music from the 1980s. <br />A total of 112 handcrafted attributes are distributed in 8 layers. The different skies, suns, clouds, grounds, horizons, and objects combined can reach up to 1,33B landscape variations. From this universe, 4,444 striking and unique art pieces were generated. A project created by <a href="https://twitter.com/rgb0x00">@rgb0x00<object id="svg1" data="/img/logo/open.svg" style="height:1em;margin-bottom:-0.2em;"></object></a></span>

                </div>

            </div>
            <div class="blog-part">

                <div class="blog-right">
                    <div class="intro">
                        <div class="mob">
                            <a class="nav-link" href="https://solsunsets.com">
                                <b>
                                    <object href="https://solsunsets.com" id="svg1" data="https://solsunsets.com/img/logo/arrow.svg" class="logo-rotate"></object>HOME
                                </b>
                            </a>

                        </div>
                        <i>
                            Here you can access all your Solsunsets banners from your wallet.
                        </i>
                    </div>
                    <!--all the config stuff-->
                    <div class="connect">
                        <ConfigPane />
                        <NFTViewForm :is-loading="isLoading" @submit-form="handleSubmitForm">
                        </NFTViewForm>
                    </div>


                </div>

            </div>
            <div id="menu" class="blog-part desk">

                <div class="blog-right-title-container"></div>
                <div class="bbutton"><a class="cyan" href="https://gallery.solsunsets.com/">SOLSUNSETS BANNERS</a></div>
                <div class="bbutton desk"><a class="cyan" href="https://solsunsets.com/memorial">SOLSUNSETS MEMORIAL</a></div>
                <div class="bbutton"><a class="cyan" href="https://up.solsunsets.com/">PIXEL ART UPSCALE TOOL</a></div>
                <div class="bbutton">MARKETS: <a class="cyan" href="https://www.magiceden.io/marketplace/solsunsets">MAGICEDEN<object id="svg1" data="https://solsunsets.com/img/logo/open.svg" style="height:1em;margin-bottom:-0.2em;"></object></a> | <a href="https://alpha.art/collection/solsunsets/">ALPHA.ART<object id="svg1" data="https://solsunsets.com/img/logo/open.svg" style="height:1em;margin-bottom:-0.2em;"></object></a></div>
                <div class="bbutton desk">
                    AUCTIONS SERIES:
                    <a class="cyan" href="https://suns.holaplex.com/listings?view=ended">HOLAPLEX <object id="svg1" data="https://solsunsets.com/img/logo/open.svg" style="height:1em;margin-bottom:-0.2em;"></object></a>
                </div>

                <div class="bbutton desk">RARITY: <a class="cyan" href="https://moonrank.app/collection/solsunsets">MOONRANK<object id="svg1" data="https://solsunsets.com/img/logo/open.svg" style="height:1em;margin-bottom:-0.2em;"></object></a> | <a class="cyan" href="https://howrare.is/solsunsets">HOWRARE<object id="svg1" data="https://solsunsets.com/img/logo/open.svg" style="height:1em;margin-bottom:-0.2em;"></object></a>  </div>


            </div>


        </div>



        <!--per NFT display-->
        <LoadingBar v-if="isLoading" :progress="progress" :text="text" class="my-5" />
        <NotifyError v-else-if="isError" class="mt-5">{{ text }}</NotifyError>
        <div v-else>
            <NFTViewCard v-for="n in NFTs" :key="n.mint" :n="n"></NFTViewCard>
        </div>

        <!--modals-->
        <!--must sit at the very bottom-->
        <div class="infinite-loading">
            <infinite-loading style="color:black;" @infinite="infiniteHandler"
                              :identifier="
        +new Date() //needs to be something thta dynamically updates, or won't work
      "
                              spinner="spiral"></infinite-loading>
        </div>
    </div>
</template>

<script lang="ts">
    import { computed, defineComponent, ref, watch } from 'vue';
    import InfiniteLoading from 'vue-infinite-loading';
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
    import ContentTooltipExport from '@/components/content/tooltip/ContentTooltipExport.vue';
    import useModal from '@/composables/modal';

    export default defineComponent({
        components: {
            ContentTooltipExport,
            ModalWindow,
            QuestionMark,
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
            const { exportJSONZip } = useDownload();
            const exportBtnText = ref(`Export ${NFTCount.value} NFTs`);
            const disableExport = ref(false);
            watch(NFTCount, (newCount) => {
                exportBtnText.value = `Export ${newCount} NFTs`;
            });

            const parseParams = (): [string, string] => {
                let returnKey: string;
                let returnPk: string;
                for (const [k, v] of Object.entries(fetchParams.value!)) {
                    if (v && v instanceof Array) {
                        returnKey = k;
                        returnPk = v[0].toBase58(); // get the first creator
                    } else if (v) {
                        returnKey = k;
                        returnPk = v.toBase58();
                    }
                }
                return [returnKey!, returnPk!];
            };

            const doneExportingCallback = () => {
                disableExport.value = false;
                exportBtnText.value = `Export ${NFTCount.value} NFTs`;
            };

            const exportNFTs = () => {
                disableExport.value = true;
                exportBtnText.value = 'preparing...';
                const allNFTs = displayedNFTs.value.concat(allFetchedNFTs.value);
                const now = +new Date();
                const [k, v] = parseParams();
                exportJSONZip(allNFTs, 'mint', `${k}-${v}-${now}`, doneExportingCallback);
            };

            // --------------------------------------- sharing
            const { copyText, setCopyText, doCopy } = useCopy();
            setCopyText('');

            const copyShareLink = async () => {
                const host = window.location.origin;
                if (fetchParams.value!.owner) {
                    await doCopy(`${host}/view/address/${fetchParams.value!.owner.toBase58()}`);
                } else if (fetchParams.value!.creator) {
                    await doCopy(`${host}/view/creator/${fetchParams.value!.creator.toBase58()}`);
                } else if (fetchParams.value!.updateAuthority) {
                    await doCopy(`${host}/view/authority/${fetchParams.value!.updateAuthority.toBase58()}`);
                } else if (fetchParams.value!.mint) {
                    await doCopy(`${host}/view/mint/${fetchParams.value!.mint.toBase58()}`);
                }
            };

            // --------------------------------------- modal
            const { registerModal, isModalVisible, showModal, hideModal } = useModal();
            registerModal('tooltipExport');

            return {
                NFTs: displayedNFTs,
                progress,
                text,
                isLoading,
                isError,
                exportNFTs,
                handleSubmitForm,
                infiniteHandler,
                // export
                exportBtnText,
                disableExport,
                // share
                copyText,
                copyShareLink,
                doCopy,
                // modal
                isModalVisible,
                showModal,
                hideModal,
            };
        },
    });
</script>

<style>
    -
    /*temp hackaround...*/
    .infinite-status-prompt {
        @apply text-white !important;
    }
</style>
