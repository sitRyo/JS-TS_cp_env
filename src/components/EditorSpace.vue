<template>
  <div id="editor-space">
    <div id="editor"></div>
  </div>
</template>

<script>
import ace from 'ace-builds'
import 'ace-builds/src-noconflict/mode-typescript'
import 'ace-builds/src-noconflict/mode-javascript'
import 'ace-builds/src-noconflict/theme-monokai'

export default {
  props: ['isExecute'],
  name: 'EditorSpace',
  data() {
    return {
      contents: 
      `//このテンプレートは崩さないでください。入力を受け取れなくなります。
function Main(input) {

}
Main(require("fs").readFileSync("/dev/stdin", "utf8"));
`,
      editor: {},
      editorId: 'editor'
    }
  },
  mounted () {
    this.editor = ace.edit( this.editorId )
    this.editor.setValue(this.contents, 1)

    this.editor.setTheme('ace/theme/monokai');
    this.editor.getSession().setMode('ace/mode/javascript')
  },
  watch: {
    'isExecute' () {
      if (this.isExecute) {
        this.contents = this.editor.getValue()
        this.$emit('exe', this.contents)
      }
    }
  }
}
</script>

<style lang="scss">
#editor {
  position: absolute;
  width: 500px;
  height: 500px;
}
</style>

