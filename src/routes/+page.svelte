<script lang="ts">
  import "../app.css";
  import Editor from "$lib/components/Editor.svelte";
  import ThemeButton from "$lib/components/ThemeButton.svelte";
  import { theme } from "$lib/theme.svelte";
  import { browser } from "$app/environment";

  let value = $state('console.log("Hello, world!");');
  let language = $state("javascript");

  let cssTheme = $derived.by(() => {
    switch (theme.value) {
      case "system":
        if (!browser) return "light";
        return window.matchMedia("(prefers-color-scheme: dark)").matches ? "dark" : "light";
      case "light":
        return "light";
      case "dark":
        return "dark";
    }
  });
</script>

<svelte:head>
  <title>Monaco Editor + SvelteKit Example</title>
</svelte:head>

<div
  class={`h-screen w-screen flex flex-col bg-white dark:bg-[#1E1E1E] text-black dark:text-white`}
  data-theme={cssTheme}
>
  <div class="p-4 flex flex-col gap-4">
    <h1 class="text-2xl">Monaco Editor + SvelteKit Example</h1>

    <div class="flex flex-col gap-2">
      <div>
        <label for="language">Language</label>
        <select class="p-2 rounded-md bg-gray-200 dark:bg-gray-700" bind:value={language}>
          <option value="plaintext">Plain Text</option>
          <option value="json">JSON</option>
          <option value="yaml">YAML</option>
          <option value="css">CSS</option>
          <option value="scss">SCSS</option>
          <option value="less">LESS</option>
          <option value="html">HTML</option>
          <option value="handlebars">Handlebars</option>
          <option value="razor">Razor</option>
          <option value="javascript">JavaScript</option>
          <option value="typescript">TypeScript</option>
        </select>
      </div>

      <div>
        <label for="theme">Theme</label>
        <ThemeButton />
      </div>
    </div>
  </div>
  <Editor {value} {language} class="flex-1" />
</div>
