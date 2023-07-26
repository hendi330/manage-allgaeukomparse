<template>
    <div class="wrap" v-if=!isFetching>
        <div class="container-fluid">
            <div class="row">
                <div :class="[navbarspace]">

                </div>
                <div :class="[contentspace]">
                    <div class="row">
                        <div class="col-md-12">
                            <h1>kontaktliste</h1>

                        </div>
                    </div>
                    <div class="row">
                        <div class="col-12 ">
                            <table class="table table-hover">
                                <thead>

                                    <tr>
                                        <th scope="col">#</th>
                                        <th scope="col">Select</th>
                                        <th scope="col">Listenname</th>
                                        <th scope="col">Erstellt am</th>


                                    </tr>
                                </thead>
                                <!-- :checked="userlist.selected" -->
                                <tbody>
                                    <tr v-for="(userlist, index) in userlists.activeLists"
                                        @click="open_modal(userlist._id)">
                                        <th scope="row">
                                            {{ index }}
                                        </th>
                                        <td>
                                            <input type="checkbox" :value="userlist.id">
                                        </td>
                                        <td>
                                            {{ userlist.listname }}
                                        </td>
                                        <td>
                                            {{ userlist.created_at }}
                                        </td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>

            </div>
        </div>
    </div>
    <!-- modal für bearbeitung einer liste. 
            Löschen, Mail, PDF -->
    

    <!-- Modal -->
    <div class="modal fade" id="listmodal" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1"
        aria-labelledby="staticBackdropLabel" aria-hidden="true">
        <div class="modal-dialog">
            <div class="modal-content">
                <div class="modal-header">
                    <h1 class="modal-title fs-5" id="staticBackdropLabel">Mach was mit der Liste</h1>
                    <button type="button" class="btn-close"  @click="close_modal"></button>
                </div>
                <div class="modal-body">
                    <table class="table">
                        <thead>
                            <tr>
                                <th scope="col">#</th>
                                <th scope="col">Name</th>
                                <th scope="col">Jobs</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="(user, index) in userlists.UsersInModal">
                                <th scope="row">{{index}}</th>
                                <td>{{ user.personalinfo_firstname }}, {{  user.personalinfo_lastname }}</td>
                                <td>Jobs: <span v-if="user.komparse">Komparse</span> <span v-if="user.umsetzer">Umsetzer</span> <span v-if="user.service">Service</span><span v-if="user.cook">Koch</span></td>
                            
                            </tr>
                            
                        </tbody>
                    </table>
                </div>
                <div class="modal-footer">
                    
                    <div class="btn-group modal-btn-group">
                    <button type="button" class="btn btn-outline-secondary" @click="close_modal">Close</button>
                    <button type="button" class="btn btn-outline-danger" @click="delete_list">Löschen</button>
                    </div><div class="btn-group modal-btn-group">
                    <button type="button" class="btn btn-outline-primary" @click="print_pdf">PDF </button>
                    <button type="button" class="btn btn-outline-primary">Mail senden</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script setup>
import { Modal } from 'bootstrap'
import { defineProps, ref, reactive, onMounted, shallowReactive,defineEmits } from 'vue';
const props = defineProps(["navbar_space", "content_space"]);
let emits = defineEmits(["print-pdf"]);
let isFetching = ref(true);
let navbarspace = ref(props.navbar_space);
let contentspace = ref(props.content_space);
let listId_userInModal= ref(null);
let userlists = reactive({
    activeLists: [],
    UsersInModal: [],
});
let modals = reactive({
    userlist: null,
});
onMounted(() => {
    fetch_userlists();
    modals.userlist = new Modal('#listmodal', {});
});
function fetch_userlists() {
    console.log("fetching userlists");
    fetch("http://localhost:5174/userlist/", {
        method: "GET",
        headers: { listname: "all", type: "all" }
    })
        .then(response => response.json())
        .then(data => {
            console.log(data);
            for (let index = 0; index < data.length; index++) {
                userlists.activeLists.push(data[index]);
                //Find the t inside string, 
                //convert date string into german date format
                userlists.activeLists[index].created_at = userlists.activeLists[index].created_at.split("T")[0];
                userlists.activeLists[index].created_at = userlists.activeLists[index].created_at.split("-").reverse().join(".");
                // userlists.activeLists[index].created_at = new Date(userlists.activeLists[index].created_at).toLocaleDateString();
            }
            isFetching.value = false;
            console.log(userlists.activeLists);
        })
}
async function open_modal(id) {
    let list = await fetch_list(id);
    let listIds = list.user_obj_list;
    listId_userInModal.value = id;
    console.log(listIds);
    for (let index = 0; index < listIds.length; index++) {
        userlists.UsersInModal.push(await fetch_user_by_id(listIds[index]));
    }
    modals.userlist.show();
}
async function fetch_list(listid) {
    let response = await fetch("http://localhost:5174/userlist/get/" + listid, {
        method: "GET",
    })
    if (response.ok) {
        let data = await response.json();
        return data; // Return the fetched data
    }

}

async function fetch_user_by_id(userid) {
    let response = await fetch("http://localhost:5174/users/get/" + userid, {
        method: "GET",
    })
    if (response.ok) {
        let data = await response.json();
        return data; // Return the fetched data
    }
}
function close_modal(){
    modals.userlist.hide();
    userlists.UsersInModal = [];
    listId_userInModal.value = null;
}

function delete_list(){
    if(listId_userInModal.value == null){
        return;
    }
    let listid = listId_userInModal.value;
    console.log("delete list");console.log(listid);
    fetch("http://localhost:5174/userlist/", {
        method: "DELETE",
        headers: {id: listid}
    });

}
function print_pdf(){
    emits("print-pdf", userlists.UsersInModal);
}
</script>