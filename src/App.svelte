<script lang="ts">
  import Gun from "gun/gun";
  import "gun/axe";
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
    text = "";
  };

  $: messages = Object.entries<any>(messagesStore).reverse();
</script>

<div class="chat">
  {#each messages as [key, message]}
    <div>
      - {new Date(message.timestamp).toLocaleTimeString()} | {message.text}
      <span>{message.userAgent}</span>
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
    overflow-y: auto;
    display: flex;
    flex-direction: column-reverse;
  }

  input {
    margin-top: 10px;
    width: 100%;
  }

  span {
    color: rgb(188, 225, 238);
  }
</style>
