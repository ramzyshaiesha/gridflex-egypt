<script setup>
import { computed, ref } from 'vue'
import { Calculator, Plus, Trash2, Zap } from 'lucide-vue-next'

const props = defineProps({ embedded: Boolean })

const applianceName = ref('')
const powerKw = ref(2)
const hoursPerDay = ref(1)
const tariff = ref(2)
const nextId = ref(3)
const appliances = ref([
  { id: 1, name: 'Washing machine', powerKw: 2, hoursPerDay: 1 },
  { id: 2, name: 'Water pump', powerKw: 1, hoursPerDay: 2 },
])

const previewKwh = computed(() => Number(powerKw.value || 0) * Number(hoursPerDay.value || 0))
const dailyKwh = computed(() => appliances.value.reduce((total, appliance) => total + appliance.powerKw * appliance.hoursPerDay, 0))
const dailyCost = computed(() => dailyKwh.value * Number(tariff.value || 0))
const monthlyCost = computed(() => dailyCost.value * 30)

function formatEstimate(value) {
  return value.toLocaleString('en-EG', { minimumFractionDigits: 1, maximumFractionDigits: 1 })
}

function addAppliance() {
  const name = applianceName.value.trim()
  if (!name || powerKw.value <= 0 || hoursPerDay.value <= 0 || hoursPerDay.value > 24) return

  appliances.value.push({
    id: nextId.value++,
    name,
    powerKw: Number(powerKw.value),
    hoursPerDay: Number(hoursPerDay.value),
  })
  applianceName.value = ''
}

function removeAppliance(id) {
  appliances.value = appliances.value.filter(appliance => appliance.id !== id)
}
</script>

<template>
  <section id="appliance-calculator" :class="['calculator-section', { 'is-embedded': props.embedded }]" aria-labelledby="calculator-title">
    <div class="calculator-shell">
      <header class="calculator-heading">
        <span class="calculator-kicker"><Calculator :size="15" /> DEMO TOOL / MODELED ESTIMATES</span>
        <h2 id="calculator-title">Appliance calculator</h2>
        <p>Estimate how much energy your appliances use and what that could cost at your chosen tariff.</p>
      </header>

      <div class="calculator-layout">
        <form class="calculator-form" @submit.prevent="addAppliance">
          <div class="calculator-form-heading">
            <div>
              <span class="calculator-overline">ADD A DEVICE</span>
              <h3>Build your daily estimate</h3>
            </div>
            <Zap :size="20" />
          </div>

          <label class="calculator-field calculator-field-wide" for="calculator-name">
            Appliance name
            <input id="calculator-name" v-model="applianceName" maxlength="50" required placeholder="e.g. Washing machine">
          </label>

          <div class="calculator-input-grid">
            <label class="calculator-field" for="calculator-power">
              Rated power
              <span class="calculator-input-wrap"><input id="calculator-power" v-model.number="powerKw" type="number" min="0.01" max="1000" step="0.01" required><small>kW</small></span>
            </label>
            <label class="calculator-field" for="calculator-hours">
              Daily usage
              <span class="calculator-input-wrap"><input id="calculator-hours" v-model.number="hoursPerDay" type="number" min="0.1" max="24" step="0.1" required><small>hours</small></span>
            </label>
          </div>

          <div class="calculator-preview"><Zap :size="17" /><span>Current entry</span><strong>{{ formatEstimate(previewKwh) }} kWh/day</strong></div>

          <label class="calculator-field calculator-field-wide" for="calculator-tariff">
            Electricity price assumption
            <span class="calculator-input-wrap"><input id="calculator-tariff" v-model.number="tariff" type="number" min="0" max="100" step="0.01"><small>EGP/kWh</small></span>
          </label>

          <button class="calculator-add" type="submit"><Plus :size="17" /> Add appliance</button>
        </form>

        <div class="calculator-results">
          <div class="calculator-results-heading">
            <div>
              <span class="calculator-overline">YOUR ESTIMATE</span>
              <h3>Daily energy profile</h3>
            </div>
            <span class="calculator-count">{{ appliances.length }} {{ appliances.length === 1 ? 'device' : 'devices' }}</span>
          </div>

          <div class="calculator-metrics">
            <div><span>Energy per day</span><strong>{{ formatEstimate(dailyKwh) }} <small>kWh</small></strong></div>
            <div><span>Estimated daily cost</span><strong>{{ formatEstimate(dailyCost) }} <small>EGP</small></strong></div>
            <div><span>Estimated 30-day cost</span><strong>{{ formatEstimate(monthlyCost) }} <small>EGP</small></strong></div>
          </div>

          <div class="calculator-list-heading"><span>REGISTERED APPLIANCES</span><span>kWh / day</span></div>
          <div v-if="appliances.length" class="calculator-list">
            <div v-for="appliance in appliances" :key="appliance.id" class="calculator-row">
              <div><strong>{{ appliance.name }}</strong><small>{{ appliance.powerKw }} kW × {{ appliance.hoursPerDay }} h/day</small></div>
              <strong class="calculator-row-kwh">{{ formatEstimate(appliance.powerKw * appliance.hoursPerDay) }}</strong>
              <button type="button" class="calculator-remove" :aria-label="`Remove ${appliance.name}`" @click="removeAppliance(appliance.id)"><Trash2 :size="16" /></button>
            </div>
          </div>
          <p v-else class="calculator-empty">No appliances added yet. Add a device to start your estimate.</p>

          <p class="calculator-disclaimer">Estimates use rated power × daily hours. Actual consumption varies by appliance and usage. Tariff and costs are illustrative; this tool does not read meters or predict bill savings.</p>
        </div>
      </div>
    </div>
  </section>
</template>

<style scoped>
.calculator-section {
  padding: 88px 7%;
  color: #14212d;
  background: #e7eef0;
  border-top: 1px solid #d6e2e5;
}

.calculator-section.is-embedded {
  padding: 0;
  background: transparent;
  border-top: 0;
}

.calculator-section.is-embedded .calculator-heading {
  margin-bottom: 20px;
}

.calculator-section.is-embedded .calculator-heading h2 {
  font-size: 32px;
}

.calculator-shell {
  max-width: 1280px;
  margin: 0 auto;
}

.calculator-heading {
  max-width: 640px;
  margin-bottom: 30px;
}

.calculator-kicker,
.calculator-overline {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  color: #168d91;
  font-size: 10px;
  font-weight: 700;
  letter-spacing: 1.6px;
}

.calculator-heading h2 {
  margin: 14px 0 10px;
  color: #14212d;
  font-family: 'Space Grotesk', sans-serif;
  font-size: clamp(32px, 4vw, 46px);
  line-height: 1.1;
}

.calculator-heading p {
  color: #566a73;
  font-size: 15px;
  line-height: 1.7;
}

.calculator-layout {
  display: grid;
  grid-template-columns: minmax(0, .88fr) minmax(0, 1.12fr);
  gap: 18px;
  align-items: stretch;
}

.calculator-form,
.calculator-results {
  min-width: 0;
  padding: 24px;
  border: 1px solid #d5e1e4;
  border-radius: 8px;
}

.calculator-form {
  display: flex;
  flex-direction: column;
  gap: 17px;
  background: #fbfdfd;
}

.calculator-form-heading,
.calculator-results-heading {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
}

.calculator-form-heading > svg {
  color: #168d91;
}

.calculator-form-heading h3,
.calculator-results-heading h3 {
  margin: 7px 0 0;
  font-family: 'Space Grotesk', sans-serif;
  font-size: 19px;
  line-height: 1.3;
}

.calculator-field {
  display: flex;
  min-width: 0;
  flex-direction: column;
  gap: 7px;
  color: #354c55;
  font-size: 12px;
  font-weight: 600;
}

.calculator-input-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
}

.calculator-field input {
  width: 100%;
  min-width: 0;
  height: 44px;
  padding: 0 12px;
  color: #14212d;
  background: #fff;
  border: 1px solid #d0dde0;
  border-radius: 5px;
  font: inherit;
  font-size: 14px;
}

.calculator-field input:focus-visible {
  outline: 2px solid #21b7ad;
  outline-offset: 2px;
}

.calculator-input-wrap {
  display: flex;
  min-width: 0;
  align-items: center;
  background: #fff;
  border: 1px solid #d0dde0;
  border-radius: 5px;
}

.calculator-input-wrap input {
  border: 0;
}

.calculator-input-wrap small {
  flex: 0 0 auto;
  padding: 0 10px 0 4px;
  color: #6c7f87;
  font-size: 10px;
}

.calculator-preview {
  display: flex;
  min-height: 44px;
  align-items: center;
  gap: 9px;
  padding: 9px 12px;
  color: #43616b;
  background: #e7f5f3;
  border: 1px solid #c8e8e3;
  border-radius: 5px;
  font-size: 11px;
}

.calculator-preview svg {
  color: #168d91;
}

.calculator-preview strong {
  margin-left: auto;
  color: #126a70;
  font-size: 12px;
  white-space: nowrap;
}

.calculator-add {
  display: inline-flex;
  min-height: 44px;
  align-items: center;
  justify-content: center;
  gap: 8px;
  color: #091a28;
  background: #e6ff5e;
  border: 0;
  border-radius: 5px;
  font: inherit;
  font-size: 13px;
  font-weight: 700;
  transition: filter .2s ease, transform .2s ease;
}

.calculator-add:hover {
  filter: brightness(.96);
  transform: translateY(-1px);
}

.calculator-results {
  color: #edf6f6;
  background: #0b1c2a;
  border-color: rgba(98, 236, 215, .22);
}

.calculator-results-heading h3 {
  color: #edf6f6;
}

.calculator-count {
  flex: 0 0 auto;
  padding: 6px 9px;
  color: #a9e7df;
  background: rgba(98, 236, 215, .08);
  border: 1px solid rgba(98, 236, 215, .2);
  border-radius: 999px;
  font-size: 10px;
}

.calculator-metrics {
  display: grid;
  grid-template-columns: repeat(3, minmax(0, 1fr));
  gap: 10px;
  margin: 22px 0;
}

.calculator-metrics > div {
  display: flex;
  min-width: 0;
  min-height: 86px;
  flex-direction: column;
  justify-content: space-between;
  gap: 12px;
  padding: 13px;
  background: rgba(255, 255, 255, .055);
  border: 1px solid rgba(228, 245, 242, .1);
  border-radius: 5px;
}

.calculator-metrics span {
  color: #afc3c9;
  font-size: 10px;
  line-height: 1.4;
}

.calculator-metrics strong {
  color: #e6ff5e;
  font-family: 'Space Grotesk', sans-serif;
  font-size: 20px;
  white-space: nowrap;
}

.calculator-metrics strong small {
  color: #c5d6d8;
  font-family: 'DM Sans', sans-serif;
  font-size: 10px;
  font-weight: 500;
}

.calculator-list-heading,
.calculator-row {
  display: grid;
  grid-template-columns: minmax(0, 1fr) 64px 32px;
  align-items: center;
  gap: 10px;
}

.calculator-list-heading {
  padding: 0 4px 9px;
  color: #90b2ba;
  border-bottom: 1px solid rgba(228, 245, 242, .16);
  font-size: 9px;
  font-weight: 700;
  letter-spacing: 1.1px;
}

.calculator-list-heading span:last-child {
  grid-column: 2;
  text-align: right;
}

.calculator-list {
  max-height: 220px;
  overflow: auto;
}

.calculator-row {
  min-height: 54px;
  border-bottom: 1px solid rgba(228, 245, 242, .09);
}

.calculator-row > div {
  display: flex;
  min-width: 0;
  flex-direction: column;
  gap: 3px;
}

.calculator-row > div strong {
  overflow: hidden;
  color: #e8f3f4;
  font-size: 12px;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.calculator-row > div small {
  color: #9ab0b9;
  font-size: 10px;
}

.calculator-row-kwh {
  color: #a7eee1;
  font-size: 12px;
  text-align: right;
}

.calculator-remove {
  display: grid;
  width: 30px;
  height: 30px;
  place-items: center;
  color: #b6c8cc;
  background: transparent;
  border: 1px solid transparent;
  border-radius: 4px;
}

.calculator-remove:hover {
  color: #ff9a83;
  background: rgba(255, 136, 109, .1);
  border-color: rgba(255, 136, 109, .24);
}

.calculator-empty,
.calculator-disclaimer {
  color: #a5b8be;
  font-size: 11px;
  line-height: 1.6;
}

.calculator-empty {
  padding: 14px 4px;
}

.calculator-disclaimer {
  margin-top: 15px;
  padding-top: 12px;
  border-top: 1px solid rgba(228, 245, 242, .12);
}

@media (max-width: 900px) {
  .calculator-section {
    padding-right: 5%;
    padding-left: 5%;
  }

  .calculator-layout {
    grid-template-columns: minmax(0, 1fr);
  }
}

@media (max-width: 520px) {
  .calculator-section {
    padding: 64px 6%;
  }

  .calculator-form,
  .calculator-results {
    padding: 18px;
  }

  .calculator-metrics {
    grid-template-columns: minmax(0, 1fr);
  }

  .calculator-metrics > div {
    min-height: 64px;
  }
}
</style>