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
    header: {
      type: Object,
      default: () => {
        return {}
      }
    },
    action: {
      type: String,
    },
    url: {
      type: String,
    },
    base64: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      editorContent: '',
      editor: null,
    }
  },
  methods: {
    editorInt (url) {
      this.editor = new Editor('#' + this.id)
      this.editor.customConfig.onchange = (html) => {
        this.editorContent = html
      }
      this.editor.customConfig.zIndex = 60

      if (this.base64) {
        // base64
        this.editor.customConfig.uploadImgShowBase64 = true
      } else {
        // 上传图片到服务器
        this.editor.customConfig.uploadImgHeaders = this.header
        this.editor.customConfig.uploadImgServer = this.action
        this.editor.customConfig.uploadImgMaxLength = 1
        this.editor.customConfig.uploadImgMaxSize = 10 * 1024 * 1024
        this.editor.customConfig.uploadFileName = 'file'
        this.editor.customConfig.customAlert = function (info) {
          this.$Modal.error({
            title: '上传失败',
            content: info
          })
        }
        this.editor.customConfig.uploadImgHooks = {
          before: function (xhr, editor, files) {
              // 图片上传之前触发
              // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，files 是选择的图片文件

              // 如果返回的结果是 {prevent: true, msg: 'xxxx'} 则表示用户放弃上传
              // return {
              //     prevent: true,
              //     msg: '放弃上传'
              // }
          },
          success: function (xhr, editor, result) {
              // 图片上传并返回结果，图片插入成功之后触发
              // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，result 是服务器端返回的结果
          },
          fail: function (xhr, editor, result) {
              // 图片上传并返回结果，但图片插入错误时触发
              // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，result 是服务器端返回的结果
          },
          error: function (xhr, editor) {
            this.$Modal.error({
              title: '上传失败',
              content: xhr.message
            })
          },
          timeout: function (xhr, editor) {
              // 图片上传超时时触发
              // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象
          },

          // 如果服务器端返回的不是 {errno:0, data: [...]} 这种格式，可使用该配置
          // （但是，服务器端返回的必须是一个 JSON 格式字符串！！！否则会报错）
          customInsert: function (insertImg, result, editor) {
            let imgurl = url + result.data.id
            insertImg(imgurl)
          }
        }
      }

      this.editor.create()
      this.editor.txt.html(this.value)
      var text = $('.w-e-text-container')
      text.css('height', this.height + 'px')
    },
  },
  mounted () {
    this.editorInt(this.url)
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
