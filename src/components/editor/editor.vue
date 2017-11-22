<template>
  <div :id="id"></div>
</template>

<script>
import Editor from 'wangeditor'
import $ from 'jquery'

export default {
  props: {
    value: {
      type: String,
      default: ''
    },
    id: {
      type: String,
      default: 'idiew-editor'
    },
    height: {
      type: Number,
      default: 600
    },
  },
  data () {
    return {
      editorContent: '',
      editor: null,
    }
  },
  methods: {
    editorInt () {
      this.editor = new Editor('#' + this.id)
      this.editor.customConfig.onchange = (html) => {
        this.editorContent = html
      }
      this.editor.customConfig.zIndex = 60
      this.editor.customConfig.uploadImgShowBase64 = true
      this.editor.create()
      this.editor.txt.html(this.value)
      var text = $('.w-e-text-container')
      text.css('height', this.height + 'px')
    },
  },
  mounted () {
    this.editorInt()
  },
  watch: {
    value (val) {
      this.editor.txt.html(val)
    },
    editorContent (val) {
      this.$emit('input', val)
    }
  }
}
</script>

<style>
  #idiew-editor{
    min-width: 800px
  }
</style>
