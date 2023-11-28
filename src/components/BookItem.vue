<script setup>
import ZondiconsBookmark from '../assets/ZondiconsBookmark.vue'
import ZondiconsList from '../assets/ZondiconsList.vue'
import BytesizeTrash from '../assets/BytesizeTrash.vue'
import { onMounted, ref } from 'vue';

const showToggle = ref(false)
const novels = ref([]);

onMounted(async () => {
      try {
        const result = await fetch(` http://localhost:3000/items`);
        if (result.ok) {
          const response = await result.json();
          novels.value = response;
        } else {
          console.error(`Failed to fetch data. Status: ${result.status}`);
        }
      } catch (error) {
        console.error('Error:', error);
      }
});

const delNovel = () => {
  novels.value = novels.value.filter(item => !itemDel.value.includes(item.id));
  numDel.value = 0;
  itemDel.value = []
  console.log(novels.value);
};


let numDel = ref(0);
const itemDel = ref([]);

const selectDelNovel = (id) => {
    const checkSameId = itemDel.value.includes(id);
    // console.log(checkSameId);

    if (checkSameId) {
        itemDel.value = itemDel.value.filter(itemId => itemId !== id);
        numDel.value -= 1;
    } else {
        itemDel.value.push(id);
        numDel.value += 1;
    }

    // console.log(numDel.value);
    // console.log(itemDel.value);
}


const cancelToggle = () => {
  showToggle.value = !showToggle.value;
  numDel.value = 0;
  itemDel.value = []
};

</script>

<template>

    <div class="max-w-[1120px] h-[100vh] mx-auto">
        <h1 class="text-left pt-10 h-24 text-2xl font-semibold pl-4 xl:pl-0">รายการที่คั่นไว้</h1>
        <hr>
        <div class="flex justify-between py-2 px-3 xl:px-0">
            <p>จำนวนทั้งหมด {{novels.length}} รายการ</p>
            <button v-if="showToggle == false" class="border px-2 py-1 rounded-2xl" v-on:click="showToggle = !showToggle">แก้ไข</button>
            <div v-if="showToggle == true">
                <button class="border px-2 py-1 rounded-2xl" v-on:click="cancelToggle()">ยกเลิก</button>
                <button class="border px-2 py-1 rounded-2xl relative" v-on:click="delNovel()"><BytesizeTrash class="absolute top-2"/><span class="pl-6">{{numDel}} รายการ</span></button>
            </div>
        </div>
        <div class="grid gap-12 w-full grid-cols-1 sm:grid-cols-2 md:grid-cols-3 px-5 xl:px-0">
            <div v-for="item in novels" :key="item.id" class="relative w-full h-full">
                <input v-on:click="selectDelNovel(item.id)" v-if="showToggle == true" id="checkToDelete" type="checkbox" class="absolute right-1 top-3">
                <div class="novel w-full flex shadow-xl bg-white rounded-xl overflow-hidden cursor-pointer hover:bg-slate-100 border hover:border-red-400">
                    <img class="w-[40%] sm:w-[30%] rounded-xl" width="100px" height="100%" :src="item.images" alt="book img" >
                        <div class="w-[60%] sm:w-[70%] px-4 py-2">  
                        <h1><b>{{ item.name }}</b></h1>
                        <p class="text-[10px]">{{ item.author }}</p>
                        <br>
                        <br>
                        <div class="flex text-[10px] relative">
                            <ZondiconsBookmark class="absolute top-1" />
                            <p class="pl-5">ตอนที่ {{item.section}} : {{ item.section_name }}</p>
                        </div>
                        <div class="flex text-[10px] relative">
                            <ZondiconsList class="absolute top-1"/>
                            <p class="pl-5">คั่นล่าสุด {{ item.bookmarked_list }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

    </div>
    
</template>

<style>
input[type=checkbox]{
  appearance: none;
  border: 2px solid orangered;
  border-radius: 50%;
  width: 15px;
  height: 15px;
  cursor: pointer;
}

input[type=checkbox]:checked {
  background-color: orangered;
}

input[type=checkbox]:checked + .novel{
  border: 2px solid orangered;
}

button:hover{
    background-color: rgb(219, 219, 219);
}

</style>