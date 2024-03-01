<template lang="pug">
div
  div.titleRow.justify-center
    span.title Vue CRUD Practice

  div.cardRow.justify-center              
    div.cardContainer
      div.searchRow.justify-start
        input(type="search" v-model='searchValue' placeholder="search...") 
        button.btnCancel(@click='clearSearch') Clear

      div.listRow
        ul.noBullets
          li.itemList.align-center.justify-space-between.flex-row(v-for='(item, key) in listData' :key='key') 
            div.itemDetails.align-center.flex-row
              span.itemIndex # {{ key + 1 }}
              
              div.itemContent.justify-space-between
                form(v-if='editableRow === item' @submit.prevent='() => editItem(key)')
                  input(v-model='editValue' :placeholder='item.content')
                  button.btnSave(type='submit') Save
                  button.btnCancel(@click='cancelEdit(item)') Cancel

                span(v-else) {{ item.content }} 

            div(v-if='searchValue === "" ').itemActions.justify-space-evenly.align-center
              div.icon(@click='onEdit(item)')
                PencilIcon
              div.icon(@click='deleteItem(key)')
                TrashIcon

          li(v-if='searchValue === "" ').addList.justify-end.align-center
            span.addContent Add new item 
            div.icon(@click='addItem')
              PlusIcon
            
</template>

<script setup>
import { ref, watch } from 'vue'
import PlusIcon from './icons/PlusIcon.vue'
import PencilIcon from './icons/PencilIcon.vue'
import TrashIcon from './icons/TrashIcon.vue'

const items = ref([
  { content: 'Attend an online interview' },
  { content: 'Prepare a resume' },
  { content: 'Solve a LeetCode Problem' },
])

const listData = ref(items.value)

const editValue = ref('')
const editableRow = ref({})

const searchValue = ref('')
const delay = ref(500)

let timer;

const debounce = (fn, delay) => {
  
  return(...args) => {
    cancelDebounce()
    
    timer = setTimeout(()=>{
      fn(...args)
    }, delay)
  }
}

const cancelDebounce = () => {
  clearTimeout(timer)
}

const updateSearch = (value) => {  

  const regex = new RegExp(value, 'i');

  const result = items.value.filter(item => 
    item.content.search(regex) >= 0 
  )  

  listData.value = result 
}

const debounceSearch = debounce(updateSearch, delay.value)

const clearSearch = () => {
  searchValue.value = ''
}

watch(searchValue, (newValue)=>{
  if(newValue){
    debounceSearch(newValue)
  }else{
    cancelDebounce()
    listData.value = items.value
  }
})

const addItem = () => {
  items.value.push({ content: `list no.${items.value.length + 1}`})
}

const onEdit = (item) => {
  editableRow.value = item 
}

const cancelEdit = (item) => {
  editableRow.value = { } 
  editValue.value = ''
}

const editItem = (key) => {
  items.value[key].content = editValue.value
  editableRow.value = { } 
  editValue.value = ''
}

const deleteItem = (key) => {
  items.value.splice(key, 1)
}
</script>

<style lang="sass" scoped>
.titleRow
  margin-top: 75px
  .title
    font-size: 40px
    font-weight: 500

.cardRow 
  .cardContainer
    width: 750px
    background-color: #fff
    margin-top: 50px
    border-radius: 12px
    padding: 20px 0px
    .searchRow
      padding: 0px 45px 20px
      border-bottom: 1px solid #000
    .listRow
      margin-top: 10px
      padding: 0px 30px
      .noBullets
        list-style-type: none
        padding: 0
        margin: 0
        li
          height: 50px
          font-size: 16px
 
        .itemList
          border-bottom: 1px solid rgb(172, 169, 169)

          .itemDetails
            width: 100%
          
            .itemIndex  
              font-size: 14px
              padding: 0px 20px
              white-space: nowrap
              
            .itemContent
              width: 100%
              height: 30px
              form
                width: 100%

          .itemActions
            width: 150px
            
.icon
  padding: 0px 10px
  cursor: pointer        

input 
  height: 30px
  width: 60%
  margin-right: 12px
  border-radius: 8px
  padding: 0px 10px

button 
  cursor: pointer
  border: 1px solid #4A3FF4
  border-radius: 8px
  padding: 0px 10px
  height: 30px
  margin: 0px 5px

.btnSave
  background-color: #4A3FF4
  color: white
.btnCancel
  color: #4A3FF4

.flex-row
  display: flex
  flex-direction: row

.justify-center
  display: flex
  justify-content: center

.justify-space-between
  display: flex
  justify-content: space-between

.justify-space-evenly
  display: flex
  justify-content: space-evenly
  
.justify-start
  display: flex
  justify-content: start

.justify-end
  display: flex
  justify-content: end

.align-center
  align-items: center
</style>
