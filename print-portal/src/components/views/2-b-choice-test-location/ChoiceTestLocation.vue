<script>
import Page from '@/components/elements/Page';
import PageIntro from '@/components/elements/PageIntro';
import PageChoice from '@/components/elements/PageChoice';
import PreferMobile from '@/components/elements/PreferMobile';
import CcModestButton from '@/components/elements/CcModestButton';
import { handleRejection } from '@/tools/error-handler';

export default {
    name: 'ChoiceTestLocation',
    components: { Page, PageIntro, PageChoice, PreferMobile, CcModestButton },
    data() {
        return {
            tooBusy: window.config.tooBusy
        }
    },
    computed: {},
    methods: {
        back() {
            this.$router.push({ name: 'ChoiceProof' })
        },
        loginWithDigid() {
            this.authNegativeTests.startAuthentication().then(() => {
                //
            }).catch(error => {
                handleRejection(error);
            })
        },
        gotoRetrieveTest() {
            this.$router.push({ name: 'ProvideCode' });
        },
        openModal() {
            this.$store.commit('modal/set', {
                messageHead: this.$t('message.info.didNotGetTestedYet.head'),
                messageBody: this.$t('message.info.didNotGetTestedYet.body'),
                closeButton: true
            })
        }
    }
}
</script>

<template>
    <Page @back="back">
        <div class="section">
            <PageIntro
                :head="$t('views.choiceTestLocation.pageHeader')"
                :intro="$t('views.choiceTestLocation.pageIntro')"/>
            <div class="section-block">
                <div class="page-choices">
                    <PageChoice
                        @select="loginWithDigid"
                        :header="$t('views.choiceTestLocation.choiceGGDHead')"
                        :body="$t('views.choiceTestLocation.choiceGGDBody')"
                        :inactive="tooBusy"/>
                    <div class="too-busy-message" v-if="tooBusy">
                        {{$t('tooBusy')}}
                    </div>
                    <PageChoice
                        @select="gotoRetrieveTest"
                        :header="$t('views.choiceTestLocation.choiceOtherLocation')"
                        :body="''"/>
                </div>
            </div>
            <div class="section-block">
                <CcModestButton
                    @select="openModal()"
                    :label="$t('views.choiceTestLocation.didNotGetTestedYet')"/>
            </div>
        </div>
        <PreferMobile/>
    </Page>
</template>

<style lang="scss">
.ChoiceTestLocation {

}
</style>
