<template>

  <v-container style="max-width: 600px;">

    <v-card class="mx-auto"  dark max-width="600">
      <v-card-title>
        <span class="text-h6 font-weight-light">Event search</span>
      </v-card-title>
      <v-card-text class="text-h5 font-weight-bold">
        <v-row>
          <v-col cols="12" md="8">
            <v-text-field v-model="searchQuery"  label="Event search"></v-text-field>
            
          </v-col>
          <v-col cols="12" md="2">
            <v-btn class="mx-0" depressed @click="search()" > Search </v-btn>
          </v-col>
        </v-row >
      </v-card-text>

    </v-card>
    <v-card class="mx-auto" color="#26c6da" dark max-width="600" style="margin-top: 1rem;">
      <v-card-text class="text-h5 font-weight-bold">
        <v-row style="padding: 1rem 0;">
          <v-btn class="mx-1" depressed @click="toUpper()" > {{ this.upperState ? 'To lower case' : 'To upper case' }} </v-btn>
          <v-btn class="mx-1" depressed @click="numerate()" > {{ this.numerateState ? 'Numerate Turn Off' : 'Numerate Turn On' }} </v-btn>
          <v-btn class="mx-1" depressed id="topdf"> to PDF </v-btn>
        </v-row >
      </v-card-text>
    </v-card>
    <v-card class="mx-auto" dark max-width="600" style="margin-top: 1rem;">
      <v-card-text class="text-h5 font-weight-bold">
        <v-row style="padding: 1rem 0;">
          <v-col cols="12" md="4">
              <v-text-field v-model="inputEvent"  label="Event"></v-text-field>
            </v-col>
            <v-col cols="12" md="3">
              <v-text-field v-model="inputDateFrom"  label="Date from" ></v-text-field>
            </v-col>

            <v-col cols="12" md="3">
              <v-text-field v-model="inputDateTill"  label="Date till"></v-text-field>
            </v-col>
            <v-col cols="12" md="2">
              <v-btn class="mx-0" depressed @click="addEvent()" > Post </v-btn>
            </v-col>
        </v-row >
      </v-card-text>
    </v-card>
    <br/>
  <div id="cvtimeline">
    <h1>
      Events
    </h1>
    <v-timeline max-width="600" dense clipped style="margin-top: 1rem;">
      <v-slide-x-transition group>
        <v-timeline-item v-for="(event, index) in timeline" :key="event.id" class="mb-4" color="pink" small >
          <v-row justify="space-between">
            <v-col cols="12" md="1" v-if="numerateState">
              <img :src="numbers['i'+index]" width="25"/>
            </v-col>
            <v-col cols="12" md="4" v-text="event.event"></v-col>
            <v-col class="text-center" cols="12" md="4" v-text="event.dateFrom + ' - ' + event.dateTill"></v-col>
            <v-col cols="12" md="2">
              <v-btn class="delete-btn" color="error" plain @click="removeEvent(event.id)">Delete</v-btn>
            </v-col>
          </v-row>
        </v-timeline-item>
        <v-timeline-item v-if="this.events.length < 1" class="mb-4" color="pink" small >
          <v-row justify="space-between">
            No records
            <v-col cols="5" v-text="'No records'"></v-col>
          </v-row>
        </v-timeline-item>
      </v-slide-x-transition>






    </v-timeline>
    </div>
    
  </v-container>

</template>

<script>

  export default {
    name: 'HelloWorld',

    data: () => ({
      numbers: {
        i0: 'https://freesvg.org/storage/img/thumb/Number-0-handwritten.png',
        i1: 'https://freesvg.org/storage/img/thumb/0-9-Handwritten-7.png',
        i2: 'https://freesvg.org/storage/img/thumb/0-9-Handwritten-6.png',
        i3: 'https://freesvg.org/storage/img/thumb/0-9-Handwritten-5.png',
        i4: 'https://freesvg.org/storage/img/thumb/0-9-Handwritten-4.png',
        i5: 'https://freesvg.org/storage/img/thumb/Number-5-handwritten.png',
        i6: 'https://freesvg.org/storage/img/thumb/0-9-Handwritten-2.png',
        i7: 'https://freesvg.org/storage/img/thumb/0-9-Handwritten-8.png',
        i8: 'https://freesvg.org/storage/img/thumb/0-9-Handwritten-1.png',
        i9: 'https://freesvg.org/img/0-9-Handwritten-9.png',
      },
      events: [
        {
          id: 1,
          event: 'Rinkodaros specas',
          dateFrom: '2019.05',
          dateTill: '2020.05',
          time: (new Date()).toTimeString()
        },
        {
          id: 2,
          event: 'Rinkodaros specas',
          dateFrom: '2021.02',
          dateTill: '2021.07',
          time: (new Date()).toTimeString()
        },
      ],
      searchResults: [],
      tmp_events: [],
      searchQuery: null,
      inputEvent: null,
      inputDateFrom: null,
      inputDateTill: null,
      nonce: 0,
      upperState: true,
      numerateState: false
    }),
    computed: {
      timeline () {
        return this.events.slice().reverse()
      },
    },
    created: function () {
      this.tmp_events = this.events
      this.nonce = this.events.length + 1
    },
    methods: {
      toPDF() {
        // var pageUrl = encodeURIComponent(window.location.href);
        // window.open('https://www.sejda.com/html-to-pdf?save-link=' + pageUrl);
      },
      toUpper() {

        if(this.upperState) {
          this.events.forEach(element => {
            element.event = element.event.toLowerCase()
          })

          this.upperState = false;

          return;
        }

        this.events.forEach(element => {
          element.event = element.event.toUpperCase()
        })

        this.upperState = true;

      },
      numerate() {

        this.numerateState ? this.numerateState = false : this.numerateState = true

      },
      search() {
        
        var year = this.searchQuery
        this.events = this.tmp_events.filter((event) => {
          var dateFrom = event.dateFrom.split('.')[0]
          var dateTill = event.dateTill.split('.')[0]

          if(year >= dateFrom && year <= dateTill) {
            return event
          }

           
          // event.dateFrom || event.dateTill == year or is in between 
        });
        console.log(this.searchResults);
      },
      addEvent() {
        const time = (new Date()).toTimeString()
        this.events.push({
          id: this.nonce++,
          event: this.inputEvent,
          dateFrom: this.inputDateFrom,
          dateTill: this.inputDateTill == null? 'Now' : this.inputDateTill,
          time: time.replace(/:\d{2}\sGMT-\d{4}\s\((.*)\)/, (match, contents) => {
            return ` ${contents.split(' ').map(v => v.charAt(0)).join('')}`
          })
        })

        this.inputEvent = null
        this.inputDateFrom = null
        this.inputDateTill = null

        this.tmp_events = this.events
      },
      removeEvent(id) {
        this.events = this.events.filter(event => event.id !== id)
      },
    }
  }
</script>
