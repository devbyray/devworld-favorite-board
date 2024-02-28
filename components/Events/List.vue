<script setup lang="ts">
const { data: events, error } = useFetch(
  'https://api.run.events/api/sessions-and-speakers/external-sessions?eventSlug=devworld-conference',
)
const myTalk = computed(() => {
    const talk = events?.data?.find((event) => event.guid === 'f711e788-9616-4cc3-9d3b-9dd6f257600f')
    console.log('talk', talk)
  return talk
})
</script>
<template>
  <!-- <pre>
        {{ events?.data }}
    </pre
  > -->
  <h2>Events</h2>
  <div v-if="myTalk">
    <h3>{{ myTalk?.title }}</h3>
    <p>{{ myTalk?.description }}</p>
  </div>
  <table v-if="events" class="text-left">
    <tr>
      <th class="border-r">#</th>
      <th class="border-r pl-2">Event</th>
      <th class="pl-2 border-r">Speaker</th>
      <th class="pl-2">Favorites</th>
    </tr>
    <tr
      v-for="(event, index) in events?.data?.sort((a, b) => b.favoriteCount - a.favoriteCount)"
      :key="event.id"
      class="border-b"
      :class="{ 'bg-green-700': event?.guid === 'f711e788-9616-4cc3-9d3b-9dd6f257600f'}"
    >
      <td class="border-r pr-1">#{{ index + 1 }}</td>
      <td class="border-r pr-1 pl-2">
        <strong>{{ event.title }}</strong>
      </td>
      <td class="pl-2 border-r">
        <p>
          <span v-for="speaker in event.speakers" :key="speaker.id"> {{ speaker.name }}, </span>
        </p>
      </td>
      <td class="pl-2">
        <strong>{{ event?.favoriteCount }}</strong>
      </td>
    </tr>
  </table>
</template>
