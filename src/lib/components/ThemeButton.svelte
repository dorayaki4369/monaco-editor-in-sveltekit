<script lang="ts">
  import { theme } from "$lib/theme.svelte";

  function toggleTheme() {
    switch (theme.value) {
      case "system":
        theme.value = "light";
        break;
      case "light":
        theme.value = "dark";
        break;
      case "dark":
        theme.value = "system";
        break;
    }
  }
</script>

<button class="p-2 rounded-md cursor-pointer bg-gray-200 dark:bg-gray-700" onclick={toggleTheme}>
  {theme.value}
</button>
