<template>
  <b-container class="bv-example-row">
    <b-row>
      <b-col>
        <editor-space 
          @exe="callbackExecuteButton"
          :is-execute="isExecute">
        </editor-space>
      </b-col>
      <b-col>
        <button @click="pushExecuteButton">Execute</button>
        <div class="io-wrapper">
          <div class="input">
            <div class="input-top">input</div>
            <textarea v-model="input"></textarea>
          </div>
          <div class="output">
            <div class="output-top">output</div>
            <textarea :value="output"></textarea>
          </div>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import EditorSpace from './EditorSpace.vue'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

export default {
  name: 'ioPos',
  data () {
    return {
      input: '',
      output: '',
      isExecute: false,

      editorContents: '',
    }
  },
  methods: {
    callbackExecuteButton: function (contents) {
      const _original_console_log = console.log
      let out = ''
      this.output = ''

      console.log = function() {
        for (let i = 0; i < arguments.length; i++) {
          _original_console_log(typeof String(arguments[i]) + ' ')
          out += String(arguments[i]) + ' '
        }

        out += '\n'
      }
      
      this.isExecute = false
      const exec = this.runCode(this.formedCode(contents))
      exec()

      this.output = out
    },

    pushExecuteButton: function () {
      this.isExecute = true
    },

    runCode: function (obj) {
      return new Function(obj)
    },

    formedCode: function (contents) {
      const inputDeclaration = 'var args = `' + this.input + '`;\n'
      const executeOrder = 'Main(args);\n'
      const insertOrder = inputDeclaration + executeOrder

      const inputOrder = 'Main(require("fs").readFileSync("/dev/stdin", "utf8"))'
      const inputOrderStart = contents.indexOf(inputOrder)

      const formedOrder = contents.substr(0,inputOrderStart-1) + '\n' + insertOrder

      return formedOrder
    }
  },
  components: {
    EditorSpace,
  }
}
</script>

<style lang="scss">
textarea {
  height: 200px;
  width: 300px;
}
</style>
