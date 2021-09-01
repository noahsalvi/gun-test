<script lang="ts">
  import Gun from "gun/gun";
  import { onMount } from "svelte";

  const gun = Gun();

  let text: string;
  let messagesStore = {};

  onMount(() => {
    gun
      .get("messages")
      .map()
      .on((data, key) => {
        messagesStore[key] = data;
        console.log(data);
      });
  });

  const submit = () => {
    const message = {
      userAgent: navigator.userAgent,
      text,
      timestamp: Date.now(),
    };
    gun.get("messages").set(message);
  };

  $: messages = Object.entries<any>(messagesStore);
</script>

<div class="chat">
  {#each messages as [key, message]}
    <div>
      - {new Date(message.timestamp).toLocaleTimeString()} | {message.text}
    </div>
  {/each}
</div>

<form on:submit|preventDefault={submit}>
  <input type="text" bind:value={text} placeholder="Hallo" />
</form>

<style>
  .chat {
    background: whitesmoke;
    padding: 10px;
    border-radius: 5px;
    height: 300px;
  }

  input {
    margin-top: 10px;
    width: 100%;
  }
</style>
