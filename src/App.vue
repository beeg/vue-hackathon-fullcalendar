<template>
  <div id="app">
    <button @click="refreshEvents">Refresh</button>
    <button v-if="selected._id" @click="removeEvent">Remove</button>
    <pre>{{ selected }}</pre>
    <full-calendar ref="calendar" :event-sources="eventSources" :resources="resourceSources" @event-selected="eventSelected" @event-created="eventCreated" :config="config"></full-calendar>
  </div>
</template>

<script>
import moment from 'moment';

export default {
  name: 'app',
  data() {
    return {
      events: [
        {
          id: 1,
          title: 'event1',
          start: moment().hours(12).minutes(0),
          resourceId: 1,
        },
        {
          id: 2,
          title: 'event2',
          start: moment().hours(9).minutes(0),
          end: moment().hours(14).minutes(0),
          resourceId: 2,
        },
        {
          id: 3,
          title: 'event3',
          start: moment().add(2, 'days'),
          end: moment().add(2, 'days').add(6, 'hours'),
          allDay: false,
          resourceId: 3,
        },
      ],

      config: {
        schedulerLicenseKey: 'GPL-My-Project-Is-Open-Source',
        defaultView: 'agendaDay',
        allDaySlot: false,
        header: {
          left: 'prev,next',
          center: 'title',
          right: 'agendaDay,agendaWeek',
        },
        eventClick: (event) => {
          this.selected = event;
        },
        eventRender: (event) => {
          console.log('eventito', event);
        },
        resources: [
          {
            id: 1,
            title: 'Room A',
          },
          {
            id: 2,
            title: 'Room B',
          },
          {
            id: 3,
            title: 'Room C',
          },
        ],
      },
      selected: {},
    };
  },

  methods: {
    refreshEvents() {
      this.$refs.calendar.$emit('refetch-events');
    },

    removeEvent() {
      this.$refs.calendar.$emit('remove-event', this.selected);
      this.selected = {};
    },

    eventSelected(event) {
      this.selected = event;
    },

    eventCreated(...test) {
      console.log(test);
    },
  },

  computed: {
    eventSources() {
      const self = this;
      return [
        {
          events(start, end, timezone, callback) {
            callback(self.events);
          },
        },
      ];
    },
    resourceSources() {
      const self = this;
      return [
        {
          resources(start, end, timezone, callback) {
            callback(self.config.resources);
          },
        },
      ];
    },
  },
};
</script>

<style>
@import '~fullcalendar/dist/fullcalendar.css';
@import '~fullcalendar-scheduler/dist/scheduler.min.css';
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
