<script>
import Identity from '@/components/elements/Identity';
import languages from '@/data/languages';
import Modal from '@/components/elements/Modal';
import { cmsDecode } from '@/tools/cms'
import Snackbar from '@/components/elements/Snackbar';
import { handleRejection } from '@/tools/error-handler';

export default {
    components: { Snackbar, Modal, Identity },
    computed: {
        dataReady() {
            return this.currentLanguage && this.$store.state.holderConfig && this.$store.state.testProviders.all.length > 0;
        },
        displaySnackbar() {
            return this.$store.state.snackbar.visible
        }
    },
    methods: {
        init() {
            this.getHolderConfig();
            this.getTestProviders();
            this.addLanguages();
            this.setCurrentLanguage();
            this.setTimerToEndSession();
        },
        async getHolderConfig() {
            this.$axios({
                method: 'get',
                url: '/holder/config'
            }).then((response) => {
                if (response.data && response.data.payload) {
                    const holderConfig = cmsDecode(response.data.payload);
                    this.$store.commit('setHolderConfig', holderConfig);
                }
            }).catch((error) => {
                handleRejection(error);
            })
        },
        async getTestProviders() {
            this.$axios({
                method: 'get',
                url: '/holder/config_providers'
            }).then((response) => {
                if (response.data && response.data.payload) {
                    const config = cmsDecode(response.data.payload);
                    this.$store.commit('testProviders/init', config.corona_test_providers);
                    this.$store.commit('eventProviders/init', config.event_providers);
                } else {
                    this.$store.commit('modal/set', {
                        messageHead: this.$t('message.error.general.head'),
                        messageBody: this.$t('message.error.general.body'),
                        closeButton: true
                    });
                }
            }).catch((error) => {
                handleRejection(error);
            })
        },
        addLanguages() {
            this.$store.commit('languages/init', languages);
        },
        setCurrentLanguage() {
            const routeNameChunks = window.location.href.split('/');
            if (routeNameChunks.length > 0) {
                const locale = routeNameChunks[3];
                const language = this.$store.getters['languages/getItemByProperty']('locale', locale, true);
                if (language) {
                    this.$store.commit('languages/setCurrent', language);
                } else {
                    this.$store.commit('languages/setCurrent', this.$store.state.languages.all[0]);
                }
            } else {
                this.$store.commit('languages/setCurrent', this.$store.state.languages.all[0]);
            }
            this.$i18n.locale = this.$store.state.languages.current.locale;
            const html = document.documentElement;
            html.setAttribute('lang', this.$store.state.languages.current.locale)
        },
        setTimerToEndSession() {
            const hours = 24;
            const time = hours * 3600000
            setTimeout(() => {
                this.$router.push({ name: 'Home' })
                this.$store.commit('sessionEnded')
                this.$store.commit('modal/set', {
                    messageHead: this.$t('message.info.sessionEnded.head'),
                    messageBody: this.$t('message.info.sessionEnded.body'),
                    closeButton: true
                })
            }, time)
        }
    },
    mounted() {
        this.init();
    }
}
</script>

<template>
    <div
        v-if="dataReady"
        id="app">
        <Identity/>
        <router-view/>
        <Snackbar v-if="displaySnackbar"/>
        <Modal/>
    </div>
</template>

<style lang="scss">
@import "@/styles/index";

* {
    box-sizing: border-box;
}
</style>
