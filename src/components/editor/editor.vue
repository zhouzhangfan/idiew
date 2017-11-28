<template>
  <div :id="id"></div>
</template>

<script>
import Editor from 'wangeditor'
import $ from 'jquery'

export default {
  props: {
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
    picLen: {
      type: Number,
      default: 100,
    },
    action: {
      type: String,
    },
    // url: {
    //   type: String,
    // },
    sevice: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      // editorContent: '',
      editor: null,
    }
  },
  methods: {
    editorInt () {
      this.editor = new Editor('#' + this.id)
      // this.editor.customConfig.onchange = (html) => {
      //   // console.log('更新了一次')
      //   this.editorContent = html
      // }
      this.editor.customConfig.zIndex = 60

      if (!this.sevice) {
        // base64
        this.editor.customConfig.uploadImgShowBase64 = true
      } else {
        // 上传图片到服务器
        this.editor.customConfig.uploadImgHeaders = this.header
        this.editor.customConfig.uploadImgServer = this.action
        this.editor.customConfig.uploadImgMaxLength = this.picLen
        this.editor.customConfig.uploadImgMaxSize = 10 * 1024 * 1024
        this.editor.customConfig.uploadFileName = 'files[]'
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
          fail: (xhr, editor, result) => {
            // 图片上传并返回结果，但图片插入错误时触发
            // xhr 是 XMLHttpRequst 对象，editor 是编辑器对象，result 是服务器端返回的结果
            this.$Modal.error({
              title: '插入图片失败',
              content: xhr.message
            })
          },
          error: (xhr, editor) => {
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
          customInsert: (insertImg, result, editor) => {
            this.$emit('on-upload-success', insertImg, result)
          }
        }
      }

      this.editor.create()
      // console.log('初始：' + this.value)
      // this.editor.txt.html(this.value)
      var text = $('#' + this.id + ' .w-e-text-container')
      text.css('height', this.height + 'px')

      // 失去焦点同步value
      // text.children('.w-e-text').blur(() => {
      //   this.$emit('on-blur', this.id, this.editorContent)
      // })
    },
    setContent (val) {
      this.editor.txt.html(val)
    },
    getContent () {
      return this.editor.txt.html()
    }
  },
  mounted () {
    this.editorInt()
  },
  watch: {
    // value (val) {
    //   console.log('变化：' + val)
    //   this.editor.txt.html(val)
    // },
    // editorContent (val) {
    //   this.$emit('input', val)
    // }
  }
}
</script>

<style>
  #idiew-editor{
    min-width: 800px
  }
</style>
