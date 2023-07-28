<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination">
      <li @click="change_page('prev')" class="page-item" :class="{ disabled: (active_page == 1 || total_pages == 0) }">
        <a class="page-link" href="#" aria-label="Previous">
          <span aria-hidden="true">&laquo;</span>
        </a>
      </li>
      <li @click="change_page(i)" class="page-item" v-for=" i in total_pages"><a class="page-link"
          :class="{ active: (active_page == i) }" href="#">{{ i }}</a></li>

      <li @click="change_page('next')" class="page-item">
        <a class="page-link" href="#" aria-label="Next"
          :class="{ disabled: (active_page == total_pages || total_pages == 0) }">
          <span aria-hidden="true">&raquo;</span>
        </a>
      </li>
    </ul>
  </nav>
</template>
<script setup>
//get limit from mainpage
//get active page from mainpage
//get total users from api
import { defineProps, ref, defineEmits, watch, toRef } from 'vue'
//evtl nochn Filter einbauen in get_total_pages. 
//so wird die Anzahl der Seiten neu berechnet, nicht nur wenn der listenType geändert wird sondern auch wenn der Filter geändert wird.

// let currenttype = ref(props.current_type);
let currenttype = toRef("1");
let limit = ref(10);
let active_page = ref(1);
let total_users = ref(1);
let total_pages = ref(1);
watch(currenttype, (newVal, oldVal) => {
  //check if in string "currenttype.value" is a comma included
  //if yes, then split it and get the first element
  //if no, then just use the string
  // console.log(currenttype.value);
  // console.log(currenttype.value.includes(","));
  console.log(currenttype.value);
  if (currenttype.value.includes(",")) {
    get_pages_jobFilterd(currenttype.value);
  }
  else {
    get_total_pages();
  }

})
// console.log(this.$el); 
get_total_pages();
const emit = defineEmits(["change-page"]);

function get_total_pages() {
  console.log(currenttype.value);
  // fetch("http://localhost:5174/users/count", {
  fetch("https://api.allgaeu-komparsen.de/users/count", {
    method: "GET",
    //headers: {limit : limit.value , page : page.value}
  })
    .then(response => response.json())
    .then(data => {
      console.log(data);
      // convert data into number

      total_users.value = data.count;
      total_pages.value = Math.ceil(total_users.value / limit.value);
      console.log(total_pages.value);
      // console.log(userlist);
      // return tmp_arr;
    })
    .catch(error => {
      console.error(error);
    });
}
//these 3 functions, changes currentpage, and emits an event which gets data from api and changes the reactive bvue array.
//muss noch coded werden.

function change_page(page) {
  console.log("change page");
  console.log(page);
  console.log(active_page.value);
  if (page == "prev" && active_page.value > 1) {
    active_page.value = active_page.value - 1;
    emit("change-page", active_page.value, limit.value);
    console.log("denke nicht");
  }
  else if (page == "next" && active_page.value < total_pages.value) {
    active_page.value = active_page.value + 1;
    console.log("hier");
    emit("change-page", active_page.value, limit.value);
  }
  else {
    if (page != "next" && page != "prev") {
      active_page.value = page;
      console.log(active_page.value);
      emit("change-page", active_page.value, limit.value);
    }
  }
}


function to_page(page) {
  active_page.value = page;
  console.log(active_page.value);
  get_total_pages();
  emit("change-page", page);
}
function prev_page() {
  if (active_page.value > 1) {
    active_page.value = active_page.value - 1;
    console.log(active_page.value);
    get_total_pages();
    emit("change-page", -1);
  }
}
function next_page() {
  if (active_page.value < total_pages.value) {
    active_page.value = active_page.value + 1;
    console.log(active_page.value);
    get_total_pages();
    emit("change-page", 1);
  }
}
function get_pages_jobFilterd(filterstring){
  
  // fetch("http://localhost:5174/users/countJobs/" + filterstring, {
    fetch("https://api.allgaeu-komparsen.de/users/countJobs/" + filterstring, {
    method: "GET",
    //headers: {limit : limit.value , page : page.value}
  })
    .then(response => response.json())
    .then(data => {
      console.log(data);
      // convert data into number

      total_users.value = data.count;
      total_pages.value = Math.ceil(total_users.value / limit.value);
      // console.log(userlist);
      // return tmp_arr;
    })
    .catch(error => {
      console.error(error);
    });
}




//page functions emits an function inside kontakte, but also changes the 
</script>