<template>

  <div id="note" class="detail">
    <note-sidebar @update:notes="val=>notes=val"/>
    <div id="note-detail">

      <div class="note-empty" v-show="!curNote.id">
        请选择笔记
      </div>

      <div v-show="curNote.id">
        <div class="note-bar">
          <span>创建日期：{{ curNote.createdAtFriendly }}</span>
          <span>更新日期：{{ curNote.updatedAtFriendly }}</span>
          <span>{{ statusText }}</span>
          <span class="iconfont icon-trash" @click="onDeleteNote" title="删除"></span>
          <span class="iconfont icon-test"   @click="isShowPreview = !isShowPreview" v-show="isShowPreview" title="编辑"></span>
          <span class="iconfont icon-chakan"   @click="isShowPreview = !isShowPreview" v-show="!isShowPreview" title="查看"></span>
        </div>
        <div class="note-title">
          <input type="text" v-model:value="curNote.title" @input="onUpdateNote"
                 @keydown="statusText='正在输入...'" placeholder="输入标题">
        </div>
        <div class="editor">
          <textarea v-show="!isShowPreview" v-model:value="curNote.content" @input="onUpdateNote"
                    @keydown="statusText='正在输入...'" placeholder="请输入内容，支持markdown语法"></textarea>
          <div class="preview markdown-body" v-html="previewContent" v-show="isShowPreview"></div>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
import NoteSidebar from "./NoteSidebar.vue";
import _ from 'lodash'
import MarkdownIt from 'markdown-it'
import {mapGetters, mapState, mapActions, mapMutations} from "vuex";


let md = new MarkdownIt()

export default {
  components: {NoteSidebar},
  data() {
    return {
      statusText: '笔记未改动',
      isShowPreview: false,
    }
  },
  created() {
    this.checkLogin({path:'/login'})
  },

  computed: {
    ...mapGetters(['notes', 'curNote']),

    previewContent() {
      return md.render(this.curNote.content || '')
    }
  },

  methods: {
    ...mapMutations(['setCurNote']),
    ...mapActions(['updateNote', 'deleteNote','checkLogin']),

    onUpdateNote: _.debounce(function () {
      this.updateNote({noteId: this.curNote.id, title: this.curNote.title, content: this.curNote.content})
        .then(data => {
          this.statusText = '已保存'
        }).catch(data => {
          this.statusText = '保存失败'
          this.$message.warning(data.msg)
        })
    }, 300),

    onDeleteNote() {
      this.deleteNote({noteId: this.curNote.id})
        .then(data => {
          this.$router.replace({path: '/note'})
        })
    }
  },

  beforeRouteUpdate(to, from, next) {
    this.setCurNote({curNoteId: to.query.noteId})
    next()
  }
}
</script>

<style lang="less" scoped>
@media (max-width: 500px) {

  #note-detail {
    //width: 100%;
    display: flex;
    align-content: space-between;
  }
}
#note-detail {
  flex: 1;
  display: flex;
  flex-direction: column;
  //height: 100vh;

  .note-detail-ct {
    height: 100%;
  }

  .note-empty {
    font-size: 50px;
    color: #ccc;
    text-align: center;
    margin-top: 100px;
  }

  .note-bar {
    height: 25px;
    padding: 4px 20px;
    border-bottom: 1px solid #eee;

    &:after {
      content: '';
      display: block;
      clear: both;
    }

    span {
      font-size: 12px;
      color: #999;
      margin-right: 4px;
    }

    .iconfont {
      float: right;
      margin-left: 4px;
      font-size: 18px;
      cursor: pointer;

    }

  }

  .note-title {
    height: 40px;
    input, span {
      display: inline-block;
      width: 100%;
      border: none;
      outline: none;
      font-size: 24px;
      padding: 10px 20px;
    }
  }

  .editor {
    position: relative;


  }

  textarea, .preview {
    position: absolute;
    width: 100%;
    padding: 20px;
    height:calc(100vh - 65px);
  }

  textarea {
    border: none;
    resize: none;
    outline: none;
    font-size: 14px;
    font-family: 'Monaco', courier, monospace;

  }

  code {
    color: #f66;
  }
}

#note {
  display: flex;
  align-items: stretch;
  background-color: #ffffff;
  flex: 1;
}


</style>
