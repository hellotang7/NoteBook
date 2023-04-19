<template>

  <div id="trash-detail">
    <div class="note-sidebar">
      <span class="add-note">回收站</span>
      <div class="menu">
        <div>更新时间</div>
        <div>标题</div>
      </div>
      <ul class="notes">
        <li v-for="note in trashNotes">
          <router-link :to="`/trash?noteId=${note.id}`">
            <span class="date">{{ note.updatedAtFriendly }}</span>
            <span class="title">{{ note.title }}</span>
          </router-link>
        </li>
      </ul>
    </div>

    <div class="note-detail">
      <div class="note-bar" v-if="true">
        <span>创建日期：{{ curTrashNote.createdAtFriendly }}</span>

        <span>更新日期：{{ curTrashNote.updatedAtFriendly }}</span>

        <span>所属笔记本：{{ belongTo }}</span>
        <a class="btn action" @click="onRevert">恢复</a>
        <a class="btn action" @click="onDelete">彻底删除</a>
      </div>

      <div class="note-title">
        <span>{{ curTrashNote.title }}</span>
      </div>
      <div class="editor">
        <div class="preview markdown-body" v-html="compiledMarkdown">{{ curTrashNote.content }}</div>
      </div>
    </div>

  </div>

</template>

<script>
import MarkdownIt from 'markdown-it'
import {mapGetters, mapActions, mapMutations} from "vuex";


let md = new MarkdownIt()


export default {
  data() {
    return {}
  },

  created() {
    this.checkLogin({path: '/login'})
    this.getNotebooks(),
    this.getTrashNotes()
      .then(() => {
        this.setCurTrashNote({curTrashNoteId: this.$route.query.noteId})
        this.$router.replace({
          path: '/trash',
          query: {noteId: this.curTrashNote.id}
        }).catch(err=>err)
      })
  },

  computed: {
    ...mapGetters([
      'trashNotes',
      'curTrashNote',
      'belongTo'
    ]),
    compiledMarkdown() {
      return md.render(this.curTrashNote.content || '')
    }
  },

  methods: {
    ...mapMutations([
      'setCurTrashNote'
    ]),
    ...mapActions([
      'checkLogin',
      'revertTrashNote',
      'deleteTrashNote',
      'getTrashNotes',
      'getNotebooks'
    ]),
    onRevert() {
      this.revertTrashNote({noteId: this.curTrashNote.id})
        .then(() => {
          this.setCurTrashNote({})
          this.$router.replace({
            path: '/trash',
            query: {noteId: this.curTrashNote.id}
          })
        })
    },
    onDelete() {
      this.$confirm('删除后将无法恢复', '确定删除吗？', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.deleteTrashNote({noteId: this.curTrashNote.id})
          .then(() => {
            this.setCurTrashNote({})
            this.$router.replace({
              path: '/trash',
              query: {noteId: this.curTrashNote.id}
            })
          })
      })

    },
  },
  beforeRouteUpdate(to, from, next) {
    this.setCurTrashNote({curTrashNoteId: to.query.noteId})
    next()
  }
}
</script>

<style lang="less" scoped>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box
}


.note-sidebar {
  background-color: #eee;
  width: 290px;
  border-right: 1px solid #ccc;

  .add-note {
    display: flex;
    justify-content: center;
    align-items: center;
    top: 8px;
    font-size: 18px;
    padding: 2px 4px;
    height: 45px;
  }

  .menu {
    border-bottom: 1px solid #ccc;
    border-top: 1px solid #ccc;
    display: flex;

    div {
      font-size: 14px;
      padding: 2px 10px;
      flex: 1;
      border-right: 1px solid #ccc;

      &:last-child {
        border-right: none;
      }
    }

    .el-icon-arrow-down {
      font-size: 10px;

    }
  }

  .notes {
    li {
      list-style: none;

      &:nth-child(odd) {
        background-color: #f2f2f2;
      }

      a {
        display: flex;
        padding: 3px 0;
        font-size: 12px;
        border: 2px solid transparent;
        text-decoration: none;
        color: #666666;
      }

      .router-link-exact-active {
        border: 2px solid #81c0f3;
        border-radius: 3px;
      }

      span {
        padding: 0 10px;
        flex: 1;
      }
    }
  }
}
@media (max-width: 500px) {
  .note-sidebar {
    width: 158px;
  }
}

.note-detail {
  flex: 1;
  display: flex;
  flex-direction: column;

  .note-detail-ct {
    //border: 1px solid red;
    //height: 100%;
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

    .btn {
      float: right;
      margin-left: 10px;
      font-size: 12px;
      cursor: pointer;
      padding: 2px 4px;
    }
    .btn:hover{
      color: #999;
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
    height:calc(100vh - 65px);
    padding: 20px;

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


#trash-detail {
  display: flex;
  align-items: stretch;
  background-color: #ffffff;
  flex: 1;
}

</style>
