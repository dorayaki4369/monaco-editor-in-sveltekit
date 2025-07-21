<script lang="ts">
  import type * as monaco from "monaco-editor";
  import { onMount } from "svelte";
  import { theme } from "$lib/theme.svelte";

  let {
    value = $bindable(""),
    language,
    ...props
  } = $props<{
    value?: string;
    language: string;
    class?: string;
  }>();

  let container: HTMLElement;
  let editor: monaco.editor.IStandaloneCodeEditor;
  let m: typeof import("monaco-editor");

  // update the language of the editor when the language changes
  $effect(() => {
    $: language;
    if (typeof editor === "undefined" || typeof m === "undefined") return;

    const model = editor.getModel();
    if (!model) return;

    $: m.editor.setModelLanguage(model, language);
  });

  // update the theme of the editor when the theme changes
  $effect(() => {
    $: theme.value;
    if (typeof m === "undefined") return;

    m.editor.setTheme(convertTheme(theme.value));
  });

  function convertTheme(theme: "system" | "light" | "dark") {
    if (theme === "system") {
      return window.matchMedia("(prefers-color-scheme: dark)").matches ? "vs-dark" : "vs-light";
    }

    return theme === "light" ? "vs-light" : "vs-dark";
  }

  async function initializeMonacoEditor() {
    m = await import("monaco-editor");

    self.MonacoEnvironment = {
      async getWorker(_, label) {
        switch (label) {
          case "json": {
            const { default: jsonWorker } = await import("monaco-editor/esm/vs/language/json/json.worker?worker");
            return new jsonWorker();
          }
          case "yaml": {
            const { default: yamlWorker } = await import("monaco-yaml/yaml.worker?worker");
            return new yamlWorker();
          }
          case "css":
          case "scss":
          case "less": {
            const { default: cssWorker } = await import("monaco-editor/esm/vs/language/css/css.worker?worker");
            return new cssWorker();
          }
          case "html":
          case "handlebars":
          case "razor": {
            const { default: htmlWorker } = await import("monaco-editor/esm/vs/language/html/html.worker?worker");
            return new htmlWorker();
          }
          case "typescript":
          case "javascript": {
            const { default: tsWorker } = await import("monaco-editor/esm/vs/language/typescript/ts.worker?worker");
            return new tsWorker();
          }
          default: {
            const { default: editorWorker } = await import("monaco-editor/esm/vs/editor/editor.worker?worker");
            return new editorWorker();
          }
        }
      },
    };

    editor = m.editor.create(container, {
      value: value,
      language: language,
      theme: convertTheme(theme.value),
    });

    editor.onDidChangeModelContent(() => (value = editor.getValue()));
  }

  onMount(() => {
    initializeMonacoEditor();

    return () => editor?.dispose();
  });
</script>

<div id="editor" bind:this={container} {...props} class={props.class}></div>
