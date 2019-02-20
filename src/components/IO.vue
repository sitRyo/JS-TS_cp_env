<template>
  <b-container class="wrapper">
    <b-row>
      <b-col>
        <editor-space 
          @exe="callbackExecuteButton"
          :is-execute="isExecute">
        </editor-space>
      </b-col>
      <b-col>
        <b-row>
          <b-col class="ml-5">
            <b-dropdown right :text="languageText">
              <b-dropdown-item v-show="!language.JavaScript" @click="switchLanguageMode('JavaScript')">JavaScript</b-dropdown-item>
              <b-dropdown-item v-show="!language.TypeScript" @click="switchLanguageMode('TypeScript')">TypeScript</b-dropdown-item>
            </b-dropdown>
          </b-col>
          <b-col class="mr-5">
            <b-button @click="pushExecuteButton">Execute</b-button>
          </b-col>
        </b-row>
        <div class="io-wrapper">
          <div class="input">
            <div class="input-top mt-4">input</div>
            <textarea v-model="input" class="input-area mt-1 mb-5 w-75"></textarea>
          </div>
          <div class="output">
            <div class="output-top mt-4">output</div>
            <textarea :value="output" class="output-area mt-1 mb-5 w-75"></textarea>
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
      languageText: 'JavaScript',
      language: {
        'JavaScript': true,
        'TypeScript': false,
      },
      languageEnum: {
        'JavaScript': 'JavaScript',
        'TypeScript': 'TypeScript',
      }
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
    },

    /* 実行言語の切り替え用メソッド。将来拡張する可能性を考えて作る
     * @param type: JavaScript or TypeScriptの文字列
     */
    switchLanguageMode: function (type) {
      for (let key in this.languageEnum) {
        if (this.language[key]) {
          this.language[key] = false
        }
      }

      this.language[type] = true
      this.languageText = this.languageEnum[type]
    }
  },
  components: {
    EditorSpace,
  }
}
</script>

<style lang="scss">

.wrapper  {
  
  .io-wrapper {
    font-weight: 600;

    textarea {
      resize: none;
      border-radius: 0.1rem;
      height: 27vh;
    }
  }
}
</style>
