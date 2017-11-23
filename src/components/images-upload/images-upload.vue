<template>
  <div class="img">
    <div class="big" :style="{backgroundImage: `url(` + url + `${selectedImg})`}">
      <div v-if="!selectedImg" class="text">
        <Icon type="images" size="100"></Icon>
        <span style="display:block;font-size:24px;margin-top:20px">暂无图片</span>
      </div>
    </div>
    <div class="small">
      <div
        class="pic"
        :class="{active:img === selectedImg}"
        v-for="(img,index) in images"
        :key="index"
        @click="selectImg(img)"
        :style="{backgroundImage: `url(` + url + `${img})`}">
          <div v-if="img === selectedImg" class="close" @click="closeImg(index)"><Icon type="close-round"></Icon></div>
      </div>
      <Upload
        ref="upload"
        :headers="header"
        v-if="images.length !== 5"
        :show-upload-list="false"
        :on-success="handleSuccess"
        :on-error="handleError"
        :format="['jpg','jpeg','png']"
        :max-size="maxSize"
        :on-format-error="handleFormatError"
        :on-exceeded-size="handleMaxSize"
        :action="action"
        style="display: inline-block">
        <div class="imgUp">
          <Icon type="arrow-up-a" size="25"></Icon>
          <span style="display:block;margin-top:5px;font-size: 14px;">上传图片</span>
        </div>
      </Upload>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'images-upload',
    props: {
      value: {
        type: Array,
        default: () => { return [] }
      },
      action: {
        type: String,
        required: true,
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
    data () {
      return {
        images: [],
        selectedImg: null
      }
    },
    methods: {
      selectImg (pic) {
        this.selectedImg = pic
      },
      closeImg (index) {
        this.$Modal.confirm({
          title: '删除图片',
          content: '确定删除图片？',
          onOk: () => {
            this.images.splice(index, 1)
            if (this.images.length !== 0) {
              this.selectedImg = this.images[0]
            } else {
              this.selectedImg = null
            }
          }
        })
      },
      handleSuccess (res, file) {
        this.images.push(res.data.id)
        this.selectedImg = res.data.id
      },
      handleError (err) {
        this.$Modal.warning({
          title: '文件上传失败',
          content: err
        })
      },
      handleFormatError (file) {
        this.$Modal.warning({
          title: '文件格式不正确',
          content: '文件 ' + file.name + ' 格式不正确，请上传 jpg 或 png 格式的图片。'
        })
      },
      handleMaxSize (file) {
        this.$Modal.warning({
          title: '超出文件大小限制',
          content: '文件 ' + file.name + ' 太大，不能超过 ' + this.maxSize/1024 + 'M。'
        })
      },
    },
    mounted () {
      this.images = this.value
    },
    watch: {
      value (val) {
        if (this.images.length === 0) {
          this.selectedImg = val[0]
        }
        this.images = val
      },
      images (val) {
        this.$emit('input', val)
      }
    }
  }
</script>

<style lang="less">
  .img{
    width: 540px;
    .big{
      width: 100%;
      /* border: 1px solid #ddd; */
      box-sizing: border-box;
      height: 360px;
      margin-bottom: 15px;
      background-color: #eee;
      text-align: center;
      background-size: cover;
      background-position: center center;
      color: #888;
      .text{
        padding-top: 100px;
      }
    }
    .small{
      display: flex;
      justify-content: flex-start;
      .pic{
        position: relative;
        width: 96px;
        box-sizing: border-box;
        height: 96px;
        border: 1px solid #ddd;
        margin-right: 15px;
        cursor: pointer;
        background-size: cover;
        background-position: center center;
        .close{
          position: absolute;
          top: 0;
          right: 0;
          width: 18px;
          height: 15px;
          color: #fff;
          background-color: #13b1b2;
          text-align: center;
          line-height: 15px;
        }
      }
      .pic:last-child{
        margin-right: 0;
      }
      .pic.active{
        border: 1px solid #13b1b2;
      }
      .pic:hover{
        border: 1px solid #13b1b2;
      }
      .imgUp{
        width: 96px;
        height: 96px;
        background-color: #eee;
        text-align: center;
        padding-top: 20px;
        color: #888;
      }
      .imgUp:hover{
        cursor: pointer;
        /* border: 1px solid #ddd; */
        box-shadow: 0px 0px 5px rgba(0,0,0,0.6)
      }
    }
  }
</style>
