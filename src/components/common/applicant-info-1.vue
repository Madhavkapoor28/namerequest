<template>
  <v-form v-model="isValid" ref="step1" id="applicant-info-1-form">
    <v-container fluid class="pa-0 mt-5" id="applicant-info-1">
      <v-row>
        <v-col cols="2" class="h6 align-self-start pt-0">Applicant</v-col>

        <v-col cols="12" md="10" lg="10" class="py-0">
          <!--FIRST NAME, MIDDLE NAME, LAST NAME-->
          <v-row>
            <v-col cols="12" md="4" lg="4" class="pt-0">
              <label for="firstname" class="hidden">First Name</label>
              <v-text-field
                :messages="messages['firstName']"
                :rules="firstMiddleNameRules"
                :value="applicant.firstName"
                @blur="handleNameBlur('firstName', $event)"
                @input="updateApplicant('firstName', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="firstname"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="First Name"
              />
            </v-col>

            <v-col cols="12" md="4" lg="4" class="pt-0">
              <label for="middlename" class="hidden">Middle Name (Optional)</label>
              <v-text-field
                :messages="messages['middleName']"
                :value="applicant.middleName"
                :rules="firstMiddleNameRules"
                @blur="handleNameBlur('middleName', $event)"
                @input="updateApplicant('middleName', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="middlename"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="Middle Name (Optional)"
              />
            </v-col>

            <v-col cols="12" md="4" lg="4" class="pt-0">
              <label for="lastname" class="hidden">Last Name</label>
              <v-text-field
                :messages="messages['lastName']"
                :rules="lastNameRules"
                :value="applicant.lastName"
                @blur="handleNameBlur('lastName', $event)"
                @input="updateApplicant('lastName', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="lastname"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="Last Name"
              />
            </v-col>
          </v-row>

          <!--ADDDRESS MENU !-->
          <v-row class="mt-n1">
            <v-col cols="12" class="py-0 my-0">
              <v-menu
                bottom
                class="pa-2"
                dense
                max-width="600"
                offset-y
                v-model="showAddressMenu"
              >
                <!-- Using activator slot to avoid having to absoluetly position v-menu. Visibility is controlled by
                "showAddressMenu" field, "on" function provided by slot is used only to supress error message-->
                <template v-slot:activator="{ on }">
                  <v-text-field
                    :messages="messages['Line1']"
                    :rules="requiredRules"
                    :value="applicant.addrLine1"
                    @blur="blurAddress1"
                    @input="updateApplicant('addrLine1', $event)"
                    dense
                    filled
                    height="50"
                    hide-details="auto"
                    id="line1"
                    :name="Math.random()"
                    autocomplete="chrome-off"
                    label="Street Address"
                    ref="Line1"
                  />
                </template>
                <v-list class="ma-0 pa-0" style="border-radius: 0">
                  <v-list-item dense v-if="!getAddressSuggestions && applicant.addrLine1" class="pa-2">
                    <v-progress-circular color="orange" id="address-suggest-spinner" indeterminate size="25" />
                    <span class="pl-2">Searching...</span>
                  </v-list-item>
                  <v-list-item
                    class="pa-2"
                    dense
                    v-if="!getAddressSuggestions && !applicant.addrLine1"
                  >
                    Start typing an address to get suggestions
                  </v-list-item>
                  <v-list-item
                    :class="getClass(address.Id)"
                    :id="address.Id"
                    :key="address.Text + '-' + i"
                    dense
                    style="cursor: pointer"
                    v-for="(address, i) of getAddressSuggestions"
                  >
                    <a
                      :ref="address.Id"
                      @click.prevent="fetchAddressDetails(address.Id)"
                      @focus="highlightedSuggestion = address.Id"
                      class="link-sm-dk-text"
                      href="#"
                    >
                      {{ address.Text + ', ' + address.Description }}
                    </a>
                  </v-list-item>

                  <v-divider class="mb-2"/>

                  <v-list-item>
                    <v-container class="ma-0 pa-0 copy-small">
                      <v-row>
                        <v-col
                          class="ma-0 px-6 pb-4 text-right"
                          align-self="center"
                        >
                          <label class="h5" for="country2">Country</label>
                        </v-col>
                        <v-col cols="5" class="ma-0 pa-0">
                          <v-select
                            :items="countryOptions"
                            :menu-props="{ auto: true, eager: true }"
                            :value="applicant.countryTypeCd"
                            @click.capture.stop
                            @input="updateApplicant('countryTypeCd', $event)"
                            class="mb-1 copy-small mr-2"
                            dense
                            eager
                            filled
                            hide-details
                            id="country2"
                            name="country2"
                            ref="Country2"
                          />
                        </v-col>
                      </v-row>
                    </v-container>
                  </v-list-item>
                </v-list>
              </v-menu>
            </v-col>
          </v-row>

          <v-row class="mt-2">
            <v-col cols="12" class="py-0 my-0">
              <label for="line2" class="hidden">Additional Street Address (Optional)</label>
              <v-text-field
                :messages="messages['Line2']"
                :value="applicant.addrLine2"
                @blur="messages = {}"
                @focus="handleFocus('Line2', 'Street Address 2 (Optional)')"
                @input="updateApplicant('addrLine2', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="1ine2"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="Additional Street Address (Optional)"
                ref="Line2"
              />
            </v-col>
          </v-row>

          <v-row class="mt-2" v-if="(applicant.addrLine2 || applicant.addrLine3) && !showAddressMenu">
            <v-col cols="12" class="py-0 my-0">
              <label for="line3" class="hidden">Additional Street Address (Optional)</label>
              <v-text-field
                :messages="messages['Line3']"
                :value="applicant.addrLine3"
                @blur="messages = {}"
                @focus="handleFocus('Line2', 'Street Address 3 (Optional)')"
                @input="updateApplicant('addrLine3', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="line3"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="Additional Street Address (Optional)"
                ref="Line3"
              />
            </v-col>
          </v-row>

          <v-row class="mt-2">
            <v-col cols="12" md="6" lg="6" class="py-0 my-0">
              <label for="city" class="hidden">City</label>
              <v-text-field
                :messages="messages['City']"
                :rules="requiredRules"
                :value="applicant.city"
                @blur="messages = {}"
                @focus="handleFocus('City', 'City')"
                @input="updateApplicant('city', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="city"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="City"
                ref="City"
              />
            </v-col>

            <v-col
              v-if="applicant.countryTypeCd === Location.CA"
              cols="12" md="6" lg="6" class="py-0 my-0"
              :class="{ 'pt-2': isMobile }"
            >
              <label for="province" class="hidden">Province</label>
              <v-select
                :items="provinceOptions"
                :messages="messages['Province']"
                :rules="requiredRules"
                :value="applicant.stateProvinceCd"
                @input="updateApplicant('stateProvinceCd', $event)"
                @blur="messages = {}"
                @focus="handleFocus('Province', 'Province')"
                dense
                filled
                height="50"
                hide-details="auto"
                id="province"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="Province"
                ref="Province"
              />
            </v-col>

            <v-col
              v-else-if="applicant.countryTypeCd === Location.US"
              cols="12" md="6" lg="6" class="py-0 my-0"
              :class="{ 'pt-2': isMobile }"
            >
              <label for="state" class="hidden">State</label>
              <v-select
                :items="$usaStateCodes"
                :messages="messages['State']"
                :rules="requiredRules"
                :value="applicant.stateProvinceCd"
                @input="updateApplicant('stateProvinceCd', $event)"
                @blur="messages = {}"
                @focus="handleFocus('State', 'State')"
                dense
                filled
                height="50"
                hide-details="auto"
                id="state"
                name="state"
                placeholder="State"
                ref="state"
              />
            </v-col>

            <v-col cols="12" md="6" lg="6" class="py-0 my-0" v-else>
              <label for="state" class="hidden">Province/State</label>
              <v-text-field
                disabled
                :messages="messages['Province']"
                :rules="provStateRules"
                :value="applicant.stateProvinceCd"
                @blur="messages = {}"
                @input="updateApplicant('stateProvinceCd', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="state"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="N/A"
                ref="state"
              />
            </v-col>
          </v-row>

          <v-row class="mt-2">
            <v-col cols="12" md="6" lg="6" class="py-0 my-0">
              <label for="country" class="hidden">Country</label>
              <v-select
                :items="countryOptions"
                :menu-props="{eager: true, auto: true}"
                :rules="requiredRules"
                :value="applicant.countryTypeCd"
                @input="updateApplicant('countryTypeCd', $event)"
                cache-items
                dense
                eager
                filled
                height="50"
                hide-details="auto"
                id="country"
                name="country"
                placeholder="Country"
                ref="Country"
              />
            </v-col>

            <v-col cols="12" md="6" lg="6" class="py-0 my-0" :class="{ 'pt-2': isMobile }">
              <label for="postalcode" class="hidden">Postal/Zip Code</label>
              <v-text-field
                :messages="messages['PostalCode']"
                :rules="requiredRules"
                :value="applicant.postalCd"
                @blur="messages = {}"
                @focus="handleFocus('PostalCode', 'Postal / Zip Code')"
                @input="updateApplicant('postalCd', $event)"
                dense
                filled
                height="50"
                hide-details="auto"
                id="postalcode"
                :name="Math.random()"
                autocomplete="chrome-off"
                label="Postal/Zip Code"
              />
            </v-col>
          </v-row>

          <v-row class="mt-2" v-if="getShowXproJurisdiction && showAllFields && getEditMode">
            <v-col cols="12" md="6" lg="6" class="py-0 my-0">
              <label for="xprojurisdiction" class="hidden">Business Jurisdiction</label>
              <v-select
                :messages="messages['xproJurisdiction']"
                :items="jurisdictionOptions"
                :rules="requiredRules"
                :value="xproJurisdiction"
                @blur="messages = {}"
                @focus="handleFocus('xproJurisdiction', 'Business xproJurisdiction')"
                @input="updateNrData('xproJurisdiction', $event)"
                dense
                eager
                filled
                height="50"
                hide-details="auto"
                id="xprojurisdiction"
                name="xprojurisdiction"
                placeholder="Business Jurisdiction"
              />
            </v-col>

            <v-col cols="6" class="py-0 my-0" />
          </v-row>

          <v-row class="mt-5">
            <v-col cols="12" md="7" lg="7" class="py-0">
              <v-checkbox
                hide-details
                label="I am completing this reservation on my own behalf"
                v-model="actingOnOwnBehalf"
                class="mt-2 pt-0"
              />
            </v-col>

            <ApplicantInfoNav @nextAction="nextAction()" />
          </v-row>
        </v-col>
      </v-row>
    </v-container>
  </v-form>
</template>

<script lang="ts">
import { Component, Vue, Watch, Mixins } from 'vue-property-decorator'
import { Action, Getter } from 'vuex-class'
import { SessionStorageKeys } from 'sbc-common-components/src/util/constants'
import ApplicantInfoNav from '@/components/common/applicant-info-nav.vue'
import { Location, NrState } from '@/enums'
import { ActionMixin } from '@/mixins'
import { ActionBindingIF } from '@/interfaces/store-interfaces'
import { NameRequestI } from '@/interfaces'
import { removeExcessSpaces } from '@/plugins/utilities'
import { CanJurisdictions, IntlJurisdictions } from '@/list-data'
import AuthServices from '@/services/auth.services'

@Component({
  components: {
    ApplicantInfoNav
  }
})
export default class ApplicantInfo1 extends Mixins(ActionMixin) {
  // enum for template
  readonly Location = Location

  // Global getters
  @Getter getActingOnOwnBehalf!: boolean
  @Getter getAddressSuggestions!: any[]
  @Getter getDisplayedComponent!: string
  @Getter getEditMode!: boolean
  @Getter getLocation!: Location
  @Getter getNr!: Partial<NameRequestI>
  @Getter getNrData!: any
  @Getter getNrState!: string
  @Getter getSubmissionTabNumber!: number
  @Getter getShowXproJurisdiction!: boolean
  @Getter getKeycloakRoles!: string[]
  @Getter isMobile!: boolean
  @Getter isRoleStaff!: boolean

  // Global actions
  @Action setActingOnOwnBehalf!: ActionBindingIF
  @Action setCorpNum!: ActionBindingIF
  @Action setNRData!: ActionBindingIF
  @Action setSubmissionTabNumber!: ActionBindingIF
  @Action setFolioNumber!: ActionBindingIF

  highlightedSuggestion: string = null
  isValid: boolean = false
  messages = {}
  provStateRules = [
    v => (typeof v === 'string') || 'Must be letters only',
    v => (!v || v.length <= 2) || 'Max 2 characters'
  ]
  lastNameRules = [
    v => !!v || 'Required field',
    v => (!v || v.length <= 50) || 'Cannot exceed 50 characters'
  ]
  firstMiddleNameRules = [
    v => (!v || v.length <= 50) || 'Cannot exceed 50 characters'
  ]
  requiredRules = [
    v => !!v || 'Required field'
  ]
  showAddressMenu: boolean = false

  async mounted () {
    // add event listener when this component is mounted
    // eg, when user continues to send for review
    this.$el.addEventListener('keydown', this.handleKeydown)

    // proceed only if we have any roles (ie, logged in), and not staff
    if (this.getKeycloakRoles.length > 0 && !this.isRoleStaff) {
      // pre-populate submitting party name
      const userInfo = await AuthServices.fetchUserInfo().catch(e => null)
      if (userInfo) {
        this.setApplicantDetails([
          { name: 'firstName', value: userInfo.firstname || '' },
          { name: 'lastName', value: userInfo.lastname || '' }
        ])
      }

      // pre-populate submitting party address
      const currentAccount = sessionStorage.getItem(SessionStorageKeys.CurrentAccount)
      if (currentAccount) {
        const accountId = JSON.parse(currentAccount)?.id
        if (accountId) {
          const orgInfo = await AuthServices.fetchOrgInfo(accountId).catch(e => null)
          if (orgInfo) {
            const mailingAddress = orgInfo.mailingAddress
            if (mailingAddress.street) {
              this.setApplicantDetails([
                { name: 'addrLine1', value: mailingAddress.street || '' },
                { name: 'addrLine2', value: mailingAddress.streetAdditional || '' },
                { name: 'addrLine3', value: '' },
                { name: 'city', value: mailingAddress.city || '' },
                { name: 'stateProvinceCd', value: mailingAddress.region || '' },
                { name: 'countryTypeCd', value: mailingAddress.country || '' },
                { name: 'postalCd', value: mailingAddress.postalCode || '' }
              ])
            }
          }
        }
      }
    }
  }

  destroyed () {
    // remove the event listener when this component is destroyed
    // eg, when user clicks Start Search Over
    this.$el.removeEventListener('keydown', this.handleKeydown)
  }

  get isVisible (): boolean {
    const myComponent = (
      this.getDisplayedComponent === 'SubmissionTabs' ||
      this.getDisplayedComponent === 'ExistingRequestEdit'
    )
    const myTab = (this.getSubmissionTabNumber === 2)
    return (myComponent && myTab)
  }

  get actingOnOwnBehalf () {
    return this.getActingOnOwnBehalf
  }

  set actingOnOwnBehalf (value) {
    if (value) this.setFolioNumber('') // no folio number in case it is on own behalf
    this.setActingOnOwnBehalf(value)
  }

  get applicant () {
    // if applicant is null/undefined then return an object
    // to prevent dereference errors (ie, cannot read property X of undefined)
    return this.getApplicant || {}
  }

  get countryOptions () {
    return IntlJurisdictions
  }

  get countryTypeCd () {
    return this.getApplicant?.countryTypeCd || ''
  }

  get jurisdictionOptions () {
    // Jurisdiction conversion to uppercase is required to match the model to the values provided by the api.
    return (this.getLocation === Location.CA)
      ? CanJurisdictions.filter(jur => jur.value !== Location.BC)
        .map(jurisdiction => ({ value: jurisdiction.text.toUpperCase(), text: jurisdiction.text }))
      : IntlJurisdictions.filter(jur => jur.value !== Location.CA)
        .map(jurisdiction => ({ value: jurisdiction.text.toUpperCase(), text: jurisdiction.text }))
  }

  get provinceOptions () {
    return CanJurisdictions.filter(jurisdiction => jurisdiction.value !== Location.FD)
      .map(jurisdiction => ({ value: jurisdiction.value, text: jurisdiction.text }))
  }

  get showAllFields (): boolean {
    return (!this.getEditMode || this.getNrState === NrState.DRAFT)
  }

  get state () {
    if (this.getNr && this.getNrState) {
      return this.getNrState
    }
    return null
  }

  get xproJurisdiction () {
    return this.getNrData?.xproJurisdiction
  }

  /*
    In order to update the trimmed input value, function setApplicationDetails( {key, value}) is also called in @input,
    which looks duplicate. However, there is something going on in the store actions/mutate code
    that requires both the blur and input events to be handled in order for the input component to be reactive.
  */
  handleNameBlur (key, event): void {
    this.messages = {}
    let value = removeExcessSpaces(event.target.value)
    this.setApplicantDetails({ key, value })
  }

  blurAddress1 () {
    this.messages = {}
  }

  clearValidation () {
    if (this.$refs.step1 as any) {
      (this.$refs.step1 as any).resetValidation()
    }
  }

  getClass (Id) {
    if (this.highlightedSuggestion) {
      if (Id === this.highlightedSuggestion) {
        return 'highlight-list-item'
      }
    }
    return ''
  }

  handleFocus (id, message) {
    this.messages[id] = message
    if (id !== 'addrLine1') {
      this.showAddressMenu = false
    }
  }

  handleKeydown (event) {
    if (!this.showAddressMenu) {
      return event
    }
    if (this.showAddressMenu && event.key === 'Escape') {
      this.showAddressMenu = false
    }
    if (this.getAddressSuggestions?.[0] && this.showAddressMenu) {
      if (event.key === 'Tab') {
        event.preventDefault()
        if (!this.highlightedSuggestion) {
          let { Id } = this.getAddressSuggestions[0]
          this.highlightedSuggestion = Id
          let ref: any = this.$refs[Id][0]
          ref.focus()
          return
        }
        if (this.highlightedSuggestion === 'Country2') {
          let { Id } = this.getAddressSuggestions[0]
          let ref: any = this.$refs[Id][0]
          ref.focus()
          return
        }
        let index = this.getAddressSuggestions?.findIndex(suggestion => suggestion.Id === this.highlightedSuggestion)
        if (index === 2) {
          this.highlightedSuggestion = 'Country2'
          let ref: any = this.$refs.Country2
          ref.focus()
          return
        }
        let { Id } = this.getAddressSuggestions[index + 1]
        let ref: any = this.$refs[Id][0]
        ref.focus()
        return
      }
      if (event.key === 'Enter' && this.highlightedSuggestion && this.highlightedSuggestion !== 'Country2') {
        event.preventDefault()
        this.fetchAddressDetails(this.highlightedSuggestion)
        this.showAddressMenu = false
      }
      return event
    }
  }

  async updateApplicant (key, value) {
    this.clearValidation()
    this.setApplicantDetails({ key, value })
    if (key === 'addrLine1') {
      this.showAddressMenu = true
      await this.fetchAddressSuggestions({ key, value })
      return
    }
    if (this.showAddressMenu && key === 'countryTypeCd') {
      this.$nextTick(async () => {
        this.showAddressMenu = true
        let appKV = {
          key: 'addrLine1',
          value: this.getApplicant?.addrLine1
        }
        await this.fetchAddressSuggestions(appKV)
      })
      return
    }
    this.showAddressMenu = false
  }

  updateNrData (key, value) {
    this.setNRData({ key, value })
  }

  validate () {
    if (this.$refs.step1 as Vue) {
      (this.$refs.step1 as any).validate()
    }
  }

  @Watch('isValid')
  onValidChanged (val: boolean) {
    if (val) {
      this.$nextTick(() => {
        if (this.$el?.querySelector instanceof Function) {
          // add classname to button text (for more detail in Sentry breadcrumbs)
          const applicantBackBtn = this.$el.querySelector('.submit-back-btn > span')
          if (applicantBackBtn) applicantBackBtn.classList.add('applicant-back-btn')
          const applicantContinueBtn = this.$el.querySelector('.submit-continue-btn > span')
          if (applicantContinueBtn) applicantContinueBtn.classList.add('applicant-continue-btn')
        }
      })
    }
  }

  nextAction () {
    this.validate()
    if (this.isValid) {
      this.setSubmissionTabNumber(this.getSubmissionTabNumber + 1)
    }
  }

  @Watch('countryTypeCd')
  handleProvince (newVal, oldVal) {
    if (newVal !== oldVal) {
      this.setApplicantDetails({ key: 'stateProvinceCd', value: '' })
    }
  }

  @Watch('showAddressMenu')
  suppressMenu (newVal) {
    if (newVal) {
      this.clearValidation()
      return
    }
    this.highlightedSuggestion = null
    let ref: any = this.$refs.Line1
    ref.focus()
  }

  @Watch('xproJurisdiction')
  watchXproJurisdiction (newVal, oldVal) {
    if (newVal !== oldVal) {
      if (this.getEditMode && newVal === this.getNr?.xproJurisdiction) {
        this.setCorpNum(this.getNr?.corpNum)
        return
      }
      this.setCorpNum('')
    }
  }

  @Watch('isVisible')
  private onVisibleChanged (val: boolean) {
    if (val) {
      // add event listener when this component is displayed
      // eg, when user comes back from next tab
      this.$el.addEventListener('keydown', this.handleKeydown)
    } else {
      // remove the event listener when this component is hidden
      // eg, when user continues to next tab
      this.$el.removeEventListener('keydown', this.handleKeydown)
    }
  }
}
</script>

<style scoped lang="scss">
@import "@/assets/styles/theme.scss";

.highlight-list-item {
  background-color: $gray2;
}
</style>
