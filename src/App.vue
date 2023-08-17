<script setup>
import { onBeforeMount, onMounted, nextTick } from 'vue';
import jsonWorker from 'monaco-editor/esm/vs/language/json/json.worker?worker'
import cssWorker from 'monaco-editor/esm/vs/language/css/css.worker?worker'
import htmlWorker from 'monaco-editor/esm/vs/language/html/html.worker?worker'
import tsWorker from 'monaco-editor/esm/vs/language/typescript/ts.worker?worker'
import EditorWorker from 'monaco-editor/esm/vs/editor/editor.worker?worker'
import * as monaco from 'monaco-editor'
onBeforeMount(() => {
  self.MonacoEnvironment = {
    getWorker(_, label) {
      if (label === 'html' || label === 'handlebars' || label === 'razor') {
        return new htmlWorker()
      }
      if (label === 'css' || label === 'scss' || label === 'less') {
        return new cssWorker()
      }
      if (['typescript', 'javascript'].includes(label)) {
        return new tsWorker()
      }
      if (label === 'json') {
        return new jsonWorker()
      }
      return new EditorWorker()
    },
  }
});

onMounted(async () => {

  await nextTick();
  console.log('123 :>> ', 123);
  monaco.languages.typescript.javascriptDefaults.setDiagnosticsOptions({
    noSemanticValidation: false,
    noSyntaxValidation: false,
  })

  monaco.languages.typescript.javascriptDefaults.setCompilerOptions({
    target: monaco.languages.typescript.ScriptTarget.ESNext,
    allowNonTsExtensions: true,
    module: monaco.languages.typescript.ModuleKind.CommonJS,
    moduleResolution: monaco.languages.typescript.ModuleResolutionKind.NodeJs,
    checkJs: true,
    // typeRoots: ["node_modules/@types"]
  })
  // Application
  // const libSource = [
  //   `declare function exportFunction(callback: (ctx: {name: string; age: number}) => void): void;`,
  // ].join('\n')
  const libSource = await fetch('koa-index.d.ts', {
    method: 'GET',
    headers: {
      "Content-Type": "text/html"
    },
  }).then((res) => res.text());
  const libUri = 'file:///node_modules/@types/koa/index.d.ts'
  monaco.languages.typescript.javascriptDefaults.addExtraLib(libSource, libUri)

  const text =
    `//import * as koa from 'koa'

app.use((ctx) => {
  ctx
})
    `

  console.log('path:', monaco.Uri.parse('file:///main.js').path);

  monaco.editor.create(document.querySelector('#editor'), {
    value: text,
    language: 'javascript',
  }, monaco.Uri.parse('file:///main.js'))


});

</script>

<template>
  <div id="editor" style="width: 100%; height: 100%">

  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}

.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}

.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
