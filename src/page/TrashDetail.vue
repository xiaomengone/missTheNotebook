<template>
  <div id="trash" class="detail">
    <div class="note-sidebar">
    <h3 class="notebook-title">回收站</h3>
    <div class="menu">
      <div>更新时间</div>
      <div>标题</div>
    </div>
    <ul class="notes">
      <li v-for="(note, index) in trashNotes" :key="index">
        <router-link :to="`/trash?noteId=${note.id}`">
          <span class="date">{{ note.updatedAtFriendly }}</span>
          <span class="title">{{ note.title }}</span>
        </router-link>
      </li>
    </ul>
  </div>
  <div class="note-detail">
      <div class="note-bar" v-if="true">
        <span> 创建日期: {{curTrashNote.createdAtFriendly}}</span> 
        <span> | </span>
        <span> 更新日期: {{curTrashNote.updatedAtFriendly}}</span>
        <span> | </span>
        <span> 所属笔记本: {{belongTo}}</span>

        <a class="btn action" @click="onRevert">恢复</a>
        <a class="btn action" @click="onDelete">彻底删除</a>
      </div>
      <div class="note-title">
        <span>{{curTrashNote.title}}</span>
      </div>
      <div class="editor">
        <div class="preview markdown-body" v-html="compiledMarkdown"></div>
      </div>
    </div>
  </div>
</template>

<script>
import Trash from '@/apis/trash.js'
import Auth from '@/apis/auth.js'
import MarkdownIt from "markdown-it";
let md = new MarkdownIt();
window.Trash = Trash
export default {
  name: "Login",
  data() {
    return {
      msg: "回收站笔记详情页",
      curTrashNote:{
        id:3,
        title:'我的笔记',
        content:'##hellow',
        createdAtFriendly:'昨天',
        updatedAtFriendly:'刚刚'
      },
      belongTo:'我的笔记本',
      trashNotes:[
        { 
          id:3,
        title:'我的笔记',
        content:'##hellow',
        createdAtFriendly:'昨天',
        updatedAtFriendly:'刚刚'
      },
        {
          id:3,
        title:'我的笔记',
        content:'##hellow',
        createdAtFriendly:'昨天',
        updatedAtFriendly:'刚刚'
        }
      ]
    };
  },
  created() {
    Auth.getInfo().then((res) => {
      if (!res.isLogin) {
        this.$router.push({ path: "/login" });
      }
    });
  },
  computed:{
    compiledMarkdown(){
      return md.render(this.curTrashNote.content || "");
    }
  },
  methods:{
    onDelete(){
      console.log('删除');
    },
    onRevert(){
      console.log('恢复');
    }
  }
};
</script>

<style lang="less" scoped>
@import url(../assets/css/noteDetail.less);
@import url(../assets/css/noteSidebar.less);
#trash {
  display: flex;
  align-items: stretch;
  background-color: #fff;
  flex: 1;

  .note-bar {
    .action {
      float: right;
      margin-left: 10px;
      padding: 2px 4px;
      font-size: 12px;

    }
  }
 }
</style>