<script>
import Print from './Print';
import PageIntro from '@/components/elements/PageIntro';
import printMixin from './print-mixin'

export default {
    name: 'PrintNegativeTest',
    components: { Print, PageIntro },
    mixins: [printMixin],
    methods: {
        back() {
            this.$router.push({ name: 'NegativeTestOverview' });
        }
    }
}
</script>

<template>
    <div class="PrintNegativeTest">
        <Print
            :type="'negativetest'"
            @back="back">
            <PageIntro
                :head="$t('views.printNegativeTest.' + pageType + '.pageHeader')"
                :intro="$t('views.printNegativeTest.' + pageType + '.pageIntro')"/>
        </Print>
    </div>
</template>
