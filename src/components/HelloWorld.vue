<template>
	<div class="hello">
      <SqlEditor ref="SqlEditor1" />
	  <br/>
	  
	   <easy-cron v-model="editCronValue"  :exeStartTime="exeStartTime"
	    :hideYear="false" :remote="null" :hideSecond="false"></easy-cron>
	</div>
</template>

<script>
	import SqlEditor from "@/components/sqlEditor/SqlEditor.vue";
	import EasyCron from '@/components/easy-cron/index'
	export default {
		components:{SqlEditor,EasyCron},
		props: {
			msg: String
		},
		data(){
			return{
				editCronValue:'',
				exeStartTime:'2020-12-12'
			}
		}
	}
</script>
<style scoped>


</style>
