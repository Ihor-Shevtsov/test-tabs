<template>
  <v-app>
    <v-tabs
      v-model='active'
      v-if='tabsData.length > 0'
      color='white'
      slider-color='cyan'
    >
      <v-tab
        v-for='(tab, index) in tabsData'
        :key='`tab${index}`'
        ripple
        :style='{ background: tab.currentColor }'
      >
        {{tab.title}} - {{tab.totalCost}}
        <v-btn flat icon color='cyan'>
          <v-icon
            @click='duplicateTab(tab)'
          >
            add_to_photos
          </v-icon>
        </v-btn>
        
        <v-btn flat icon color='cyan'>
          <v-icon
            @click='removeTab(tab.id)'
          >
            delete_outline
          </v-icon>
        </v-btn>

      </v-tab>
      <v-tab-item
        class='tabContent'
        v-for='(tab, index) in tabsData'
        :key='`tabContent${index}`'
      >
        <v-card flat>
          <v-form v-model="tab.valid">
            <v-text-field
              outline
              label='Title'
              v-model='tab.title'
              :rules='[rules.required]'
            ></v-text-field>

            <v-select
              outline
              :items='tab.services'
              v-model='tab.selectedService'
              @change='tab.currentColor = getTabColor(tab.selectedService)'
              label='Service'
            ></v-select>

            <v-text-field
              outline
              label='Quantity'
              type='number'
              v-model='tab.quantity'
              :rules='[rules.required, rules.positive]'
              @change='calculateTotal(tab)'
            ></v-text-field>

            <v-text-field
              outline
              label='Unit cost'
              type='number'
              v-model='tab.unitCost'
              :rules='[rules.required, rules.positive]'
              @change='calculateTotal(tab)'
            ></v-text-field>
          </v-form>
        </v-card>
      </v-tab-item>
    </v-tabs>

    <div class='text-xs-center mt-3'>
      <v-btn @click='addDefaultTab'>Add tab</v-btn>
    </div>
    <div class='text-xs-center mt-3'>
      <v-btn @click='showInJson = !showInJson'>Show in JSON</v-btn>
    </div>

    <vue-json-pretty
      v-if='showInJson'
      :data="tabsData"
    ></vue-json-pretty>
  </v-app>
</template>

<script>
  import VueJsonPretty from 'vue-json-pretty';

  const defaultTab = {
    id: generateId(),
    title: 'Tab title',
    services: ['Service 1', 'Service 2', 'Service 3'],
    selectedService: 'Service 1',
    quantity: 0,
    unitCost: 0,
    totalCost: 0,
    currentColor: 'white',
    valid: true,
  };

  const colorScheme = {
    'Service 1': 'white',
    'Service 2': 'lightgreen',
    'Service 3': 'lightgoldenrodyellow',
  };

  function generateId () {
    return Math.random().toString(36).substr(9);
  }

export default {
  name: 'App',
  components: {
    VueJsonPretty,
  },
  data () {
    return {
      active: null,
      tabsData: [],
      value: '',
      rules: {
        required: value => !!value || 'Required.',
        positive: value => value >= 0 || 'Only zero and positive numbers.',
      },
      showInJson: false,
    }
  },
  created() {
    this.addDefaultTab();
  },
  methods: {
    addDefaultTab() {
      this.tabsData.push(this.copyTabObject(defaultTab));
    },
    duplicateTab(tab) {
      this.tabsData.push(this.copyTabObject(tab));
    },
    removeTab(id) {
      this.tabsData = this.tabsData.filter(tab => tab.id != id);
    },
    copyTabObject(tab) {
      return {...tab, id: generateId()};
    },
    getTabColor(service) {
      return colorScheme[service];
    },
    calculateTotal(tab) {
      if(!tab.valid) return;
      tab.totalCost = tab.quantity * tab.unitCost;
    },
    kurl(...args) {
      console.log(args)
    }
  }
}
</script>
<style>
  #app {
    background: white;
  }
  .tabContent {
    margin: 0 auto;
    width: 50%;
  }
</style>
