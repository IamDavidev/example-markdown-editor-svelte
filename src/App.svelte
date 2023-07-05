<script lang="ts">
  import { marked } from 'marked';
  import { onMount } from 'svelte';

  import type MonacoType from 'monaco-editor';
  import mdWorker from 'monaco-editor/esm/vs/basic-languages/markdown/markdown?worker';

  let monacoEditorElement: HTMLElement | null;
  let monacoEditor: MonacoType.editor.IStandaloneCodeEditor;
  let MonacoInstance: typeof MonacoType;
  let valueMonacoEditor: string = '# Hello, world!';

  interface Token {
    token: string;
    foreground: string;
    background?: string;
  }

  export const rules: Token[] = [
    {
      token: 'keyword.md',
      foreground: '#EF596F',
    },
    {
      token: 'strong.md',
      foreground: '#D8985F',
    },
    {
      token: 'emphasis.md',
      foreground: '#d55fde',
    },
    {
      token: 'string.link.md',
      foreground: '#d55fde',
    },
  ];

  onMount(async () => {
    //@ts-ignore
    self.MonacoEnvironment = {
      getWorker(workerId: any, label: string) {
        if (label === 'markdown') {
          return new mdWorker();
        }
      },
    };

    MonacoInstance = await import('monaco-editor');
    MonacoInstance.editor.defineTheme('vs-dark-custom', {
      base: 'hc-black',
      inherit: true,
      colors: {
        'editor.background': '#111111',
      },
      rules,
    });

    monacoEditor = MonacoInstance.editor.create(monacoEditorElement, {
      value: '# Hello, world!',
      language: 'markdown',
    });

    monacoEditor.updateOptions({
      theme: 'vs-dark-custom',
    });

    monacoEditor.onDidChangeModelContent(() => {
      valueMonacoEditor = monacoEditor.getValue();
    });

    return () => {
      monacoEditor.dispose();
    };
  });
</script>

<main class="container">
  <div bind:this={monacoEditorElement} class="monaco-editor" />
  <div class="preview">
    {@html marked(valueMonacoEditor)}
  </div>
</main>

<style>
  .container {
    display: flex;
    flex-direction: row;
  }

  .monaco-editor {
    height: 100vh;
    width: 50vw;
  }
  .preview {
    height: 100vh;
    width: 50vw;
    background-color: #111;
    color: #fff;
    padding: 1rem;
    box-sizing: border-box;
  }
</style>
