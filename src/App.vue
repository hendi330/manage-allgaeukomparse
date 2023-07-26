<script setup>

</script>

<template>
   <main>
      <headervue @change-page="change_main_page" @toggle-list-icon="toggle_list_icon"/>
      <kontakte  
      v-if="page == 'kontakte'" 
            navbar_space="col-md-3 col-lg-2 d-md-block" 
            content_space="col-md-9 col-lg-10"
            :isMobil = isMobile
            @print-pdf="HTML_to_pdf" 
            @get-user-by-id="fetch_user_by_id" 
            @toggle-list-icon="toggle_list_icon"
            @get-filter = "get_filter_array"
            @set-filter = "set_filter_array"
            @set-filterval = "set_filter_val"
            @reset-filter = "reset_filter_array"
            :filterArray = "filter"
         />
      <kontaktliste
      v-if="page == 'kontaktliste'" 
         navbar_space="col-md-3 col-lg-2 d-md-block"
         content_space="col-md-9 col-lg-10"
         @print-pdf="HTML_to_pdf"  />
      <profil v-if="page == 'profil'" navbar_space="col-md-3 col-lg-2 d-md-block" content_space="col-md-9 col-lg-10" />

   </main>
</template>
<script setup>
import kontakte from './components/mainpages/kontakte.vue'
import kontaktliste from './components/mainpages/kontaktliste.vue'
import profil from './components/mainpages/profil.vue'
import headervue from './components/headervue.vue'
import { jsPDF } from "jspdf";
import { ref ,reactive} from 'vue'
import { onUpdated,onBeforeMount ,onMounted} from 'vue'

import { provide } from 'vue';
let page = ref('kontakte');
let filter = reactive([]); //filter object
let isMobile = ref(false);
function change_main_page(newPage) {
    page.value = newPage;
    console.log(page.value);
}
onBeforeMount(() => {
   console.log("hi");
   let navlinks = document.getElementsByClassName("nav-link");
    for(let i = 0; i < navlinks.length; i++){
      if(navlinks[i].classList.contains("active")){
      console.log(navlinks[i].id);
        
      }
    }
})
onMounted(() => {
   //check if page is mobile or not
   let screenWidth = window.innerWidth || document.documentElement.clientWidth || document.body.clientWidth;
   if(screenWidth < 768){
      isMobile.value = true;
   }
   else{
      isMobile.value = false;
   }
})
async function HTML_to_pdf(userIDS){
   var pdf = new jsPDF('p', 'px', 'a4');
   console.log(userIDS[0]._id);
   //wrapper DIV for each User
   let main = document.createElement('div');
   let users_per_page = 4;
   let currentPage = 1;
   pdf.setPage(currentPage);

   //perso Info tabelle
   //get user by UserID
   let users = [];

   for(let i = 0; i < userIDS.length; i++){
      let userdata = await fetch_user_by_id(userIDS[i]._id);
      // let json = await response.json();
      users.push(userdata);
   }
   for(let i = 0; i < users.length; i++){
      console.log(users[i]);
      let tmp_div = document.createElement('div');
      tmp_div.style.display = 'flex';
      tmp_div.style.width = "100%";
      tmp_div.style.marginBottom = "30px";
      let list = document.createElement('ul');
      list.style.fontSize = "8px";
      list.style.lineHeight = "10px";
      list.style.listStyleType = "none";
      list.style.minWidth = "150px";

      list.appendChild(create_li_header_element("Pesonalinfos"));
      list.appendChild(create_hr_element());
      //decode utf-8 to string
      
      list.appendChild(create_li_element("Vorname", decodeURIComponent(users[i].personalinfo_firstname)));
      list.appendChild(create_li_element("Nachname", decodeURIComponent(users[i].personalinfo_lastname)));
      list.appendChild(create_li_element("Geburtstag", users[i].personalinfo_birthdate));
      list.appendChild(create_li_element("Telefon", users[i].personalinfo_phonenumber));
      list.appendChild(create_li_element("EMail", decodeURIComponent(decodeURIComponent(decodeURIComponent(users[i].personalinfo_email)))));
      list.appendChild(create_li_header_element("AdressInfos"));
      list.appendChild(create_hr_element());
      list.appendChild(create_li_element("Stadt", decodeURIComponent(users[i].location_city) +", "+ users[i].location_zipcode));
      list.appendChild(create_li_element("Straße", decodeURIComponent(users[i].location_street) + ", " + users[i].location_housenumber));
      tmp_div.appendChild(create_img_elemet(users[i].personalinfo_pic));
      tmp_div.appendChild(list);
      tmp_div.appendChild(create_img_elemet(users[i].personalinfo_perso));
      // let add_to_pdf =tmp_div.cloneNode(true);
      
      main.appendChild(tmp_div.cloneNode(true));
   //    if (i % users_per_page === 0 && i !== 0) {
   //  // Add a new page
      // pdf.html(main,{
      //    x: 12,
      //    y: 12,
      //       callback: function () {
      // // Add the page number to each page (optional)
      //       pdf.setPage(currentPage);
      //       // pdf.text(`Page ${currentPage}`, 200, 290);
      //       }
      //  });
      //    pdf.addPage();
      // }
      // main = document.createElement('div');
      if(i % users_per_page === 0 && i !== 0){
         pdf.addPage();
       currentPage++;

         pdf.setPage(currentPage);
         
         // currentPage++;
      }
      }
      pdf.html(main, {
  x: 12,
  y: 12,
  callback: function () {
    // Save the PDF
    pdf.save('newpdf.pdf');
  }
});
   //    let currentPage = 1;
   // for(let i = 0; i < main.children.length; i++){
      
   //    console.log(main.children[i]);
   //    pdf.html(main.children[i],{
   //       x: 12,
   //       y: 12,
   //          callback: function () {
   //    // Add the page number to each page (optional)
   //          pdf.setPage(currentPage);
   //          pdf.text(`Page ${currentPage}`, 200, 290);
   //          }
   //     });
     
   // }
   console.log(pdf);
   
   // pdf.save('newpdf.pdf');
//  console.log(main);
   
//image of user
   // pdf.html(main , {
   //             callback: function(pdf) {
   //                // pdf.setFontSize(10);
   //                pdf.save("newpdf.pdf");
   //             },
   //             x: 12,
   //             y: 12
   //          });  
    
}
function create_img_elemet(src){
   let img = document.createElement('img');
   img.src = src;
   img.style.width = "100px";
   img.style.height = "100px";
   img.style.marginLeft = "20px";
   return img;
}
function create_li_element(text, info){
   let li = document.createElement('li');
   let span = document.createElement('span');
   span.style.whiteSpace = "nowrap";
   span.innerHTML = text +":"+ info;
   li.appendChild(span);
   return li;

}
function create_hr_element(){
   let hr = document.createElement('hr');
   hr.style.marginTop = "5px";
   hr.style.marginBottom = "5px";
   return hr;
}
function create_li_header_element(text){
   let li = document.createElement('li');
   li.style.fontSize = "12px";
   li.style.fontWeight = "bold";
   li.innerHTML = text;
   return li;

}
async function fetch_user_by_id(id){
   try{
      let response = await fetch("http://localhost:5174/users/get/" +id, {
        method: "GET",
        //headers: {limit : limit.value , page : page.value}
      });
      if (response.ok) {
         let data = await response.json();
         return data; // Return the fetched data
      } 
    else {
         throw new Error('Failed to fetch user data');
      }
   } 
  catch (error) {
    console.error(error);
    // Handle the error appropriately
  
   }
        
}
function toggle_list_icon(){
    if(page.value == "kontakte" ){
      console.log("toggle");
      let icon = document.getElementById("buttondiv");
      icon.classList.toggle("hideme");
    }
   
}
function get_filter_array(){
   let tmpFilter ={
      hello : "world"
   };
   
   return filter;
}
function set_filter_val(key, value){
  

   	filter[key] = value;
      console.log(filter);
}
function set_filter_array(keys, values){
   	for(let i = 0; i < keys.length; i++){
   		filter[keys[i]] = values[i];
   	}
}
function reset_filter_array(){
      //step through ech filter entry and set it to null
      for (let key in filter) {
         if(!(key == "cloth_sizes")){

            filter[key] = "";
         }
         else {
            filter[key] = [];
         }
      }
   }



const referenceFetchFunction = ref( fetch_user_by_id);
   
provide ('fetch_user_by_idBRUh', referenceFetchFunction); 

   </script>
 

