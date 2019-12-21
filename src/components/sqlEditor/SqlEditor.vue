<template>
	<textarea ref="txt" class="txt" />
	</template>

<script>
import CodeMirror from "codemirror";
import "codemirror/lib/codemirror.css";
import "codemirror/theme/neo.css";
import "codemirror/theme/panda-syntax.css";
import "codemirror/mode/sql/sql.js";

import "codemirror/addon/hint/show-hint.js";
import "codemirror/addon/hint/show-hint.css";
import "./sql-hint.css";
//import "codemirror/addon/hint/sql-hint.js";
// import { post } from "@/utils/request.js";
export default {
  name: "SqlEditor",
  data() {
    return {
      editor: null,
      SqlList:[],//数据库
       dico: [//sql  pf库  table表  column字段
        { className: "sql", text: "SELECT" },
        { className: "sql", text: "FROM" },
        { className: "sql", text: "WHERE" },
        { className: "sql", text: "INNER" },
        { className: "sql", text: "JOIN" },
        { className: "sql", text: "UNION" },
        { className: "sql", text: "EXEC" },
        { className: "sql", text: "INSERT" },
        { className: "sql", text: "INTO" },
        { className: "sql", text: "VALUES" },
        { className: "sql", text: "UPDATE" },
        { className: "sql", text: "DELETE" },
        { className: "sql", text: "GROUP" },
        { className: "sql", text: "BY" },
        { className: "sql", text: "HAVING" },
        { className: "sql", text: "IS" },
        { className: "sql", text: "DISTINCT" },
        { className: "sql", text: "OUTER" },
        { className: "sql", text: "TOP" },
        { className: "sql", text: "EXISTS" },
        { className: "sql", text: "WHEN" },
        { className: "sql", text: "CASE" },
        { className: "sql", text: "CAST" },
        { className: "sql", text: "IN" },
        { className: "sql", text: "NULL" },
        { className: "table", text: "te_cash_exch" },
        { className: "column", text: "status_cash" },
        { className: "pf", text: "AddParamAtos." },
      ]
    };
  },
  beforeDestroy() {
    if (this.editor) this.editor = null;
  },
  methods: {
    initInfo(e = "") {//设置值
      this.editor.setValue(e);
    },
    returnInfo() {//return editor为页面操作
      return this.editor;
    },
    /*
    Return a list of suggestion base on the searchString (the current word that user is typing).
    Each suggestion is an object {text, displayText, className}. See https://codemirror.net/doc/manual.html#addon_show-hint
    - keywords start with the searchString appears first in the suggestion list
    */
    suggest(searchString) {
      /*
      we will score which suggesion should appears first, the higer the score, the higer is the appearance order
      */
      let token = searchString;
      if (searchString.startsWith(".")) token = searchString.substring(1);
      else token = searchString.toLowerCase();
      let resu = [];
      let N = this.dico.length;

      //init scoring: only retains and score suggestions which contain the searchString
      for (let i = 0; i < N; i++) {
        let keyword = this.dico[i].text.toLowerCase();
        let suggestion = null;
        //the base score of all the suggestion is N-i (it means we respect the order in the dico)
        if (keyword.startsWith(token)) {
          //add N to the score of keywords which begin with the token to make them raise up in the suggestion list
          suggestion = Object.assign({ score: N + (N - i) }, this.dico[i]);
        } else if (keyword.includes(token)) {
          suggestion = Object.assign({ score: N - i }, this.dico[i]);
        }
        if (suggestion) resu.push(suggestion);
      }

      //case suggestion for "."
      if (searchString.startsWith(".")) {
        //raise score of columns, decrease the score of sql keyword
        resu.forEach(s => {
          if (s.className == "column") s.score += N;
          else if (s.className == "sql") s.score -= N;
          return s;
        });
      }

      //console.log(searchString);
      return resu.sort((a, b) => b.score - a.score);
    },
    /*
    [hint implementation for codemirror](https://codemirror.net/doc/manual.html#addon_show-hint):
    take an editor instance and options object, and return a {list, from, to} object, where list is an array of strings or objects (the completions), and from and to give the start and end of the token that is being completed as {line, ch} objects. 
     */
    hint(editor) {
      let cur = editor.getCursor();
      let token = editor.getTokenAt(cur);
      let searchString = token.string;
      return {
        list: this.suggest(searchString),
        from: CodeMirror.Pos(cur.line, token.start),
        to: CodeMirror.Pos(cur.line, token.end)
      };
    },
    //实时SQL数据库,表,字段
     init(){
        const _this = this
        this.editor.on('cursorActivity', function(editor) {
        const __Cursor = editor.getDoc().getCursor()
        const __Token = editor.getTokenAt(__Cursor)
        const { string } = __Token
        if (string.charAt(string.length - 1) === '.') {
          // 当输入点时
          const curIndex = __Token.start
          const curLine = _this.editor.getLine(__Cursor.line)
          const key = curLine.slice(curLine.slice(0, curIndex).lastIndexOf(' ') + 1, curIndex) // 点前的关键字
           // let index =_this.SqlList.findIndex(item => item.name == key);
           // _this.SqlList.map(i=>{
           //   i.type='0'
           // })
           _this.getSqltable(_this.SqlList[index])
        }else{
           // _this.SqlList.map(i=>{
           //   i.type='1'
           // })
        }
      })
     },
    //实时数据请求
    async getSqltable(item) {
		//模拟数据table
		 let sqlTableList=[{name:"TEST"},{name:"TEST1"},{name:"TEST2"}]
         let arr=this.dico;
         let newArr = arr.reduce((total, current) => {
              current.className !== "table" && total.push(current);
              return total;
          }, []);
         //暂时bug 判断加点
         let type='1';//不加
         this.SqlList.map(i=>{ type=i.type;});
         sqlTableList.map(i=>{
            newArr.unshift({className:'table',text: type=='0' ? '.'+i.name :i.name})
          });
          this.dico=newArr;
          this.editor.showHint()
    }
  },
  mounted() {
    if (this.editor) this.editor = null;
    this.editor = CodeMirror.fromTextArea(this.$refs.txt, {
      tabSize: 4,
      mode: "text/x-mysql",
      theme: "panda-syntax",
      lineNumbers: true,
      line: true,
      lineWrapping: true,
      hintOptions: {
        completeSingle: false,
        hint: this.hint
      },
      extraKeys: {
        "Ctrl-Space": editor => {
          editor.showHint();
        }
      }
    });
    this.editor.on("keypress", editor => {
      editor.showHint();
    });
    this.init();
    this.getSqlList();
  }
};
</script>

<style scoped>
.txt {
  width: 100%;
  height: 100%;
}
</style>
