<template>
<br>
  <div>
    <h1>分类管理一哈哈</h1>
    <n-button @click="showAddModel=true">添加</n-button>
    <n-table :bordered="false" :single-line="false">
        <thead>
          <tr>
            <th>编号</th>
            <th>名称</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(category,index) in categoryList">
            <td>{{category.id}}</td>
            <td>{{category.name}}</td>
            <td>
                <n-space>
                    <n-button @click="toUpdate(category)">修改</n-button>
                    <n-button @click="deleteit(category)">删除</n-button>
                </n-space>
            </td>
          </tr>
          <tr>
            <td>阿索</td>
            <td>比哇</td>
            <td>扣扣马得哒</td>
          </tr>
        </tbody>
      </n-table>

      <n-modal v-model:show="showAddModel" preset="dialog" title="Dialog">
        <template #header>
          <div>添加分类</div>
        </template>
        <div>
            <n-input v-model:value="addCategory.name" type="text" placeholder="输入名称"/>
        </div>
        <template #action>
          <div>
            <n-button @click="add">提交</n-button>
          </div>
        </template>
      </n-modal>
    
      <n-modal v-model:show="showUpdateModel" preset="dialog" title="Dialog">
        <template #header>
          <div>修改分类</div>
        </template>
        <div>
            <n-input v-model:value="updateCategory.id" type="text" placeholder="输入修改的id"/>
            <br>
            <n-input v-model:value="updateCategory.name" type="text" placeholder="输入名称"/>
        </div>
        <template #action>
          <div>
            <n-button @click="update">提交</n-button>
          </div>
        </template>
      </n-modal>

  </div>
</template>

<script setup>
import {ref,reactive,inject,onMounted} from 'vue'
import axios from 'axios'
// const axios = inject("axios")
import {AdminStore} from '../../stores/AdminStore'
import { useRoute,useRouter } from 'vue-router';
const router = useRouter()
const route = useRoute()

const message = inject("message")
const dialog = inject("dialog")
axios.defaults.baseURL="http://localhost:9000"

const adminStore = AdminStore()

const categoryList = ref([]);

const showAddModel = ref(false);
const showUpdateModel = ref(false);

const addCategory = reactive({
    name:""
})

const updateCategory = reactive({
    id: "",
    name:""
})

onMounted(() => {
    loadDatas();
})


const loadDatas = async ()=>{
    let res = await axios.get("/category/list")
    categoryList.value = res.data.rows
    // console.log(res)
}

const add= async()=>{
    let res = await axios.post("/category/_token/add",{name:addCategory.name})
    console.log(res)
    console.log(adminStore.token)
    if(res.data.code == 200){
        loadDatas();
        message.success(res.data.msg)
        
    }else{
        message.error(res.data.msg)
    }
    showAddModel.value = false;
}


const update= async()=>{
    let res = await axios.put('/category/_token/update',{id:updateCategory.id,name:updateCategory.name})
    if(res.data.code == 200){
        loadDatas();
        message.success(res.data.msg)
        
    }else{
        message.error(res.data.msg)
    }
    showUpdateModel.value = false;
}

const toUpdate = async (category)=>{
    showUpdateModel.value = true
    updateCategory.id = category.id
    updateCategory.name = category.name
}

const deleteit= async(category)=>{

    dialog.warning({
          title: '警告',
          content: '你确定？',
          positiveText: '确定',
          negativeText: '算了算了',
          onPositiveClick: async () => {
            let res = await axios.delete(`/category/_token/delete?id=${category.id}`)
            if(res.data.code == 200){
                loadDatas();
                message.success(res.data.msg)
            }else{
                message.error(res.data.msg)
            }
            message.success('好吧👌')
          },
          onNegativeClick: () => {
            message.error('算咯👌')
          }
        })
   
}



</script>

<style>

</style>