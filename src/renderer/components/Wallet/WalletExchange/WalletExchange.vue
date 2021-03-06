<template>
  <section>
    <div
      v-if="!isPurchaseEnabled"
      class="mx-auto max-w-md flex flex-col items-center py-5"
    >
      <a
        href="#"
        alt="Changelly"
        @click.stop="openChangellyWebsite"
      >
        <img
          :src="changellyLogo"
          alt="Changelly"
        >
      </a>

      <div class="leading-normal mt-10 mb-5">
        <p class="font-semibold mb-2 text-center">
          {{ $t('PAGES.WALLET_EXCHANGE.CHANGELLY_TERMS.TITLE', { ticker: currentTicker }) }}
        </p>
        <p class="text-justify">
          {{ $t('PAGES.WALLET_EXCHANGE.CHANGELLY_TERMS.CONTENT', { ticker: currentTicker }) }}
        </p>
      </div>

      <InputSwitch
        v-model="isChangellyEnabled"
        :is-reverse="true"
        :is-large="false"
      >
        <i18n
          path="PAGES.WALLET_EXCHANGE.CHANGELLY_TERMS.CONFIRMATION"
          tag="div"
          class="ml-4 leading-normal"
        >
          <a
            place="terms"
            href="#"
            @click.stop="openChangellyTerms"
          >
            {{ $t('PAGES.WALLET_EXCHANGE.CHANGELLY_TERMS.TERMS_OF_USE') }}
          </a>
          <a
            place="privacy"
            href="#"
            @click.stop="openChangellyPrivacyPolicy"
          >
            {{ $t('PAGES.WALLET_EXCHANGE.CHANGELLY_TERMS.PRIVACY_POLICY') }}
          </a>
          <a
            href="#"
            place="kyc"
            @click.stop="openChangellyKYC"
          >
            {{ $t('PAGES.WALLET_EXCHANGE.CHANGELLY_TERMS.KYC') }}
          </a>
          <span place="button">
            "{{ $t('COMMON.CONFIRM') }}"
          </span>
        </i18n>
      </InputSwitch>

      <div class="text-center mt-10">
        <button
          :disabled="!isChangellyEnabled"
          class="blue-button"
          @click="setConfirmed"
        >
          {{ $t('COMMON.CONFIRM') }}
        </button>
      </div>
    </div>

    <div
      v-else
      class="WalletExchange__iframe mx-auto overflow-hidden mt-5"
    >
      <iframe
        :src="changellyWidgetURL"
        width="100%"
        height="550"
        class="changelly overflow-y-hidden border-none"
        scrolling="no"
      />
    </div>
  </section>
</template>

<script>
import { InputSwitch } from '@/components/Input'
import { MARKET } from '@config'

export default {
  name: 'WalletExchange',

  components: {
    InputSwitch
  },

  data: () => ({
    isChangellyEnabled: false,
    isPurchaseEnabled: false
  }),

  computed: {
    hasDarkTheme () {
      return this.$store.getters['session/hasDarkTheme']
    },

    currentNetwork () {
      return this.session_network
    },

    currentTicker () {
      return this.currentNetwork.market.ticker
    },

    currentCurrency () {
      return this.session_currency
    },

    changellyLogo () {
      const filename = this.hasDarkTheme ? 'changelly-logo-dark' : 'changelly-logo'
      return this.assets_loadImage(`pages/${filename}.png`)
    },

    changellyWidgetURL () {
      const to = this.currentNetwork.token
      const address = this.wallet_fromRoute.address
      const from = this.currentCurrency
      const amount = MARKET.crypto.includes(from) ? 1 : 300
      const theme = 'aqua'

      const baseUrl = 'https://widget.changelly.com?fiat=true&'
      const params = `from=${from.toLowerCase()}&to=${to.toLowerCase()}&address=${address}&amount=${amount}&theme=${theme}`

      return baseUrl + params
    }
  },

  deactivated () {
    this.isPurchaseEnabled = false
    this.isChangellyEnabled = false
  },

  methods: {
    openChangellyWebsite () {
      this.electron_openExternal('https://changelly.com')
    },

    openChangellyTerms () {
      this.electron_openExternal('https://changelly.com/terms-of-use')
    },

    openChangellyPrivacyPolicy () {
      this.electron_openExternal('https://changelly.com/privacy-policy')
    },

    openChangellyKYC () {
      this.electron_openExternal('https://changelly.com/aml-kyc')
    },

    setConfirmed () {
      this.isPurchaseEnabled = true
    }
  }
}
</script>

<style scoped>
.WalletExchange__iframe {
  width: 550px;
}
</style>
