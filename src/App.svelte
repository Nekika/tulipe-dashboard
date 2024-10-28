<script lang="ts">
  type Event = { datetime: string; type: string };

  let events = $state<Event[]>([]);

  const promise = fetch("http://localhost:9898/events")
    .then((response) => response.json())
    .then((data) => (events = data))
    .finally(() => {
      new WebSocket("ws://localhost:9898/events/stream").addEventListener(
        "message",
        (message) => {
          const event: Event = JSON.parse(message.data);
          events = [event, ...events];
        },
      );
    });
</script>

<main>
  <h3>Events</h3>
  {#await promise}
    Loading events...
  {:then}
    <ul>
      {#each events as event}
        <li>[{event.datetime}] {event.type}</li>
      {/each}
    </ul>
  {/await}
</main>

<style>
</style>
