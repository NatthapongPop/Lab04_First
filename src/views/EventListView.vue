<template>
  <h1>Events For Good</h1>
  <div class="events">
    <EventCard
      v-for="event in events"
      :key="event.id"
      :event="event"
    ></EventCard>

    <div class="pagination">
      <router-link
        id="page-prev"
        :to="{ name: 'EventListView', query: { page: page - 1 } }"
        rel="prev"
        v-if="page != 1"
      >
        Prev
      </router-link>

      <router-link
        id="page-next"
        :to="{ name: 'EventListView', query: { page: page + 1 } }"
        rel="next"
        v-if="hasNextPage"
      >
        Next
      </router-link>

      <router-link
        :to="{
          name: 'EventListView',
          query: { perPage: perPage - 1, page: page }
        }"
      >
        -
      </router-link>

      <router-link
        :to="{
          name: 'EventListView',
          query: { perPage: perPage + 1, page: page }
        }"
      >
        +
      </router-link>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import EventCard from '@/components/EventCard.vue'
import EventService from '@/services/EventService.js'
import { watchEffect } from '@vue/runtime-core'

export default {
  name: 'EventListView',
  props: {
    page: {
      type: Number,
      required: true
    },
    perPage: {
      type: Number,
      required: true
    }
  },
  components: {
    EventCard
  },
  data() {
    return {
      events: null,
      totalEvents: 0
    }
  },
  created() {
    watchEffect(() => {
      EventService.getEvents(this.perPage, this.page)
        .then((response) => {
          this.events = response.data
          this.totalEvents = response.headers['x-total-count']
        })
        .catch((error) => {
          console.log(error)
        })
    })
  },
  computed: {
    hasNextPage() {
      let totalPages = Math.ceil(this.totalEvents / this.perPage)

      return this.page < totalPages
    }
  }
}
</script>
<style scoped>
.events {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.pagination {
  display: flex;
  width: 290px;
}
.pagination a {
  flex: 1;
  text-decoration: none;
  color: #2c3e50;
}

#page-prev {
  text-align: left;
}
#page-next {
  text-align: right;
}
</style>
