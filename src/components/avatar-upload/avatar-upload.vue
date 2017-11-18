<template>
  <Upload
    ref="upload"
    :action="action"
    :headers="header"
    :max-size="maxSize"
    accept="image/*"
    :format="['jpg','jpeg','png']"
    :on-exceeded-size="handleErrorSize"
    :show-upload-list="false"
    :on-success="handleSuccess"
    style="display:inline-block">
    <div class="idiew-dashed" v-if="!imgId">
      <Icon :size="20" type="arrow-up-a"></Icon>
      <p>点击上传</p>
    </div>
    <div class="idiew-picture" v-else :style="{backgroundImage: `url('${url + '' +imgId}')`}">
      <Icon class="idiew-picture-icon" :size="20" type="arrow-up-a"></Icon>
      <p class="idiew-picture-text">重新上传</p>
    </div>
  </Upload>
</template>

<script>
export default {
  name: 'avatar-upload',
  props: {
    value: {
      type: String
    },
    action: {
      type: String,
      required: true
    },
    url: {
      type: String,
      required: true
    },
    header: {
      type: Object,
      default: () => { return {} }
    },
    maxSize: {
      type: Number,
      default: 10240
    }
  },
  mounted () {
    this.imgId = this.value
  },
  data () {
    return {
      imgId: null,
    }
  },
  methods: {
    handleSuccess (res) {
      this.imgId = res.data.id
    },
    handleErrorSize () {
      this.$Modal.error({
        title: '上传图片过大',
        content: '请上传小于' + (this.maxSize / 1024) + 'M的文件！'
      })
    }
  },
  watch: {
    value (val) {
      this.imgId = val
    },
    imgId (val) {
      this.$emit('input', val)
    }
  }
}
</script>

<style>
  .idiew-dashed{
    height: 120px;
    width: 90px;
    border: 1px dashed #ccc;
    border-radius: 5px;
    transition: all 500ms;
    color: #ccc;
    text-align: center;
    padding-top: 30px;
    font-size: 14px;
  }
  .idiew-dashed:hover{
    cursor: pointer;
    border-color: #fb0c18
  }
  .idiew-picture{
    height: 120px;
    width: 90px;
    border-radius: 5px;
    border: 1px solid transparent;
    transition: all 500ms;
    text-align: center;
    padding-top: 30px;
    color: #fb0c18;
    font-size: 14px;
    background-position: center center;
    background-size: cover;
  }
  .idiew-picture .idiew-picture-icon{
    transition: all 500ms;
    opacity: 0;
  }
  .idiew-picture .idiew-picture-text{
    transition: all 500ms;
    opacity: 0;
  }
  .idiew-picture:hover{
    border-color: #fb0c18;
    cursor: pointer;
  }
  .idiew-picture:hover .idiew-picture-icon{
    opacity: 1;
  }
  .idiew-picture:hover .idiew-picture-text{
    opacity: 1;
  }
</style>
