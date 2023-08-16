<script setup>
import {onBeforeMount, onMounted, nextTick} from 'vue';
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
    noSemanticValidation: true,
    noSyntaxValidation: false,
  })

  monaco.languages.typescript.javascriptDefaults.setCompilerOptions({
    target: monaco.languages.typescript.ScriptTarget.ESNext,
    allowNonTsExtensions: true,
    checkJs: true,
  })
  // Application
  const libSource = [
    `declare function exportFunction(callback: (ctx: {name: string; age: number}) => void): void;`,
  ].join('\n')
  const libUri = 'myTypes.d.ts'
  monaco.languages.typescript.javascriptDefaults.addExtraLib(libSource, libUri)

  const text = `export default (ctx) => {
  ctx.name = '111'
}
  `
  monaco.editor.create(document.querySelector('#editor'), {
    value: text,
    language: 'javascript',
    automaticLayout: true, // 自适应布局
    theme: 'vs', // 官方自带三种主题vs, hc-black, or vs-dark
    foldingStrategy: 'indentation',
    renderLineHighlight: 'all', // 行亮
    selectOnLineNumbers: true, // 显示行号
    minimap: {
      enabled: true, // 是否代码缩略图
    },
    readOnly: false, // 只读
    fontSize: 14, // 字体大小
    scrollBeyondLastLine: false, // 取消代码后面一大段空白
    overviewRulerBorder: false, // 不要滚动条的边框
    tabSize: 2, // 锁进字符
    autoIndent: 'keep', // 自动缩进
  })
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
