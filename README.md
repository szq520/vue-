# default

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Run your tests
```
npm run test
```

### Lints and fixes files
```
npm run lint
```

### 基于vue,codemirror SQL
```
npm install --save codemirror
 组件components/sqlEditor
  页面使用
  import SqlEditor from "@/components/sqlEditor/SqlEditor.vue";
 <div>
  <SqlEditor ref="SqlEditor1" />
 </div>
```

### 基于vue,iview Cron时间表达式
```
npm install --save cron-parser debounce
 组件components/easy-cron
  页面使用
 <easy-cron v-model="editCronValue"  :exeStartTime="exeStartTime"
  :hideYear="false" :remote="null" :hideSecond="false"></easy-cron>
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
