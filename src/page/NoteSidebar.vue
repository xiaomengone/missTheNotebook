<template>
  <div class="note-sidebar">
    <span class="btn add-note" @click="addNote">添加笔记</span>
    <el-dropdown
      class="notebook-title"
      @command="handleCommand"
      placement="bottom"
    >
      <span class="el-dropdown-link">
        {{ curBook.title }} <i class="iconfont icon-down"></i>
      </span>
      <!-- 下拉框 -->
      <el-dropdown-menu slot="dropdown">
        <el-dropdown-item
          v-for="(notebook, index) in notebooks"
          :command="notebook.id"
          :key="index"
          >{{ notebook.title }}</el-dropdown-item
        >
        <el-dropdown-item command="trash">回收站</el-dropdown-item>
      </el-dropdown-menu>
    </el-dropdown>
    <div class="menu">
      <div>更新时间</div>
      <div>标题</div>
    </div>
    <ul class="notes">
      <li v-for="(note, index) in notes" :key="index">
        <router-link :to="`/note?noteId=${note.id}&notebookId=${curBook.id}`">
          <span class="date">{{ note.updatedAtFriendly }}</span>
          <span class="title">{{ note.title }}</span>
        </router-link>
      </li>
    </ul>
  </div>
</template>
  
  <script>
import Notebooks from "@/apis/notebooks";
import Notes from "@/apis/notes";
import Bus from "@/Http/bus";
window.Notes = Notes;
export default {
  created() {
    Notebooks.getAll()
      .then((res) => {
        this.notebooks = res.data;
        this.curBook =
          this.notebooks.find(
            (notebook) => notebook.id == this.$route.query.notebookId
          ) ||
          this.notebooks[0] ||
          {};
        return Notes.getAll({ notebookId: this.curBook.id });
      })
      .then((res) => {
        this.notes = res.data;
        this.$emit("update:notes", this.notes);
        Bus.$emit("update:notes", this.notes);
      });
  },

  data() {
    return {
      notebooks: [],
      notes: [],
      curBook: {},
    };
  },
  // props: ["curNote"],
  methods: {
    handleCommand(notebookId) {
      if (notebookId == "trash") {
        return this.$router.push({ path: "/trash" });
      }

      this.curBook = this.notebooks.find(
        (notebook) => notebook.id == notebookId
      );

      Notes.getAll({ notebookId }).then((res) => {
        this.notes = res.data;
        //子传父
        this.$emit("update:notes", this.notes);
      });
    },
    addNote() {
      Notes.addNote({ notebookId: this.curBook.id }).then((res) => {
        console.log(res);
        this.notes.unshift(res.data);
      });
    },
  },
};
</script>
  
  <style lang="less" scoped >
@import url(../assets/css/noteSidebar.less);
</style>
  
  
  