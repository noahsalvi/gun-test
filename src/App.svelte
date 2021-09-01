<script lang="ts">
  import Gun from "gun/gun";
  import "gun/axe";
  import { onMount } from "svelte";

  const gun = Gun();

  let text: string;
  let messagesStore = {};

  function hash(str) {
    str ||= "";

    return str
      .split("")
      .reduce(
        (prevHash, currVal) =>
          ((prevHash << 5) - prevHash + currVal.charCodeAt(0)) | 0,
        0
      );
  }

  onMount(() => {
    gun
      .get("messages")
      .map()
      .on((data, key) => {
        messagesStore[key] = data;
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
    <div class="message">
      {new Date(message.timestamp).toLocaleTimeString()}
      <span>#{message.userAgent ? hash(message.userAgent) : "No Agent"}</span>
      <div>
        {message.text || "[Empty]"}
      </div>
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
    height: 50vh;
    overflow-y: auto;
    display: flex;
    flex-direction: column-reverse;
  }

  .message {
    margin-top: 10px;
  }

  input {
    margin-top: 10px;
    width: 100%;
  }

  span {
    margin-left: 10px;
    color: rgb(187, 185, 185);
  }
</style>
