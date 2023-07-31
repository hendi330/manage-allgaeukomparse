<template>
    <div class="container-fluid">
        <div class="row">
            <div :class="[navbarspace]">

            </div>
            <div :class="[contentspace]">
                <div class="row">
                    <div class="col-md-12">
                        <h1>kontakte</h1>
                    </div>
                    <div class="col-12 col-lg-6">
                        <div class="btn-group">

                            <button class="btn btn-outline-warning" @click="open_filter_modal">
                                User filtern
                            </button>

                        </div>
                    </div>


                </div>
                <div class="row">
                    <div class="col-12 ">
                        <table class="table table-hover">
                            <thead>

                                <tr>

                                    <th scope="col">#</th>
                                    <th scope="col">A</th>
                                    <th scope="col" @click="sort_users_str('firstname')">VN</th>
                                    <th scope="col" @click="sort_users_str('lastname')">LN</th>

                                    <th scope="col" @click="sort_users_num('age')">Age
                                    </th>
                                    <th scope="col" @click="sort_users_str('email')">Email</th>
                                    <th v-if="check_for_filter()" scope="col">checked</th>

                                    <th scope="col">Info</th>


                                </tr>
                            </thead>
                            <tbody>





                                <tr v-for="(user, index) in userlist.users">

                                    <th scope="row" @click="toggle_checkbox(index)">
                                        {{ index }}
                                    </th>
                                    <td>
                                        <input :id="`checkbox_${index}`" type="checkbox" v-model="user.selected"
                                            @click="toggle_checkbox_inputfield(index)">
                                    </td>

                                    <td>
                                        {{ user.firstname }}
                                    </td>
                                    <td>
                                        {{ user.lastname }}
                                    </td>
                                    <td>
                                        {{ calculate_age(user.birthdate) }}
                                    </td>
                                    <td :id="decode_3times(user.email)"
                                        @click="open_email_modal('EmailById', decode_3times(user.email))">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor"
                                            class="bi bi-envelope" viewBox="0 0 16 16">
                                            <path
                                                d="M0 4a2 2 0 0 1 2-2h12a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2H2a2 2 0 0 1-2-2V4Zm2-1a1 1 0 0 0-1 1v.217l7 4.2 7-4.2V4a1 1 0 0 0-1-1H2Zm13 2.383-4.708 2.825L15 11.105V5.383Zm-.034 6.876-5.64-3.471L8 9.583l-1.326-.795-5.64 3.47A1 1 0 0 0 2 13h12a1 1 0 0 0 .966-.741ZM1 11.105l4.708-2.897L1 5.383v5.722Z" />
                                        </svg>
                                    </td>
                                    <td v-if="check_for_confirmed(user) && check_for_filter()">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
                                            class="bi bi-person-fill-check icon-green" viewBox="0 0 16 16">
                                            <path
                                                d="M12.5 16a3.5 3.5 0 1 0 0-7 3.5 3.5 0 0 0 0 7Zm1.679-4.493-1.335 2.226a.75.75 0 0 1-1.174.144l-.774-.773a.5.5 0 0 1 .708-.708l.547.548 1.17-1.951a.5.5 0 1 1 .858.514ZM11 5a3 3 0 1 1-6 0 3 3 0 0 1 6 0Z" />
                                            <path
                                                d="M2 13c0 1 1 1 1 1h5.256A4.493 4.493 0 0 1 8 12.5a4.49 4.49 0 0 1 1.544-3.393C9.077 9.038 8.564 9 8 9c-5 0-6 3-6 4Z" />
                                        </svg>
                                    </td>
                                    <td v-if="!check_for_confirmed(user) && check_for_filter()">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor"
                                            class="bi bi-person-fill-exclamation icon-warning" viewBox="0 0 16 16">
                                            <path
                                                d="M11 5a3 3 0 1 1-6 0 3 3 0 0 1 6 0Zm-9 8c0 1 1 1 1 1h5.256A4.493 4.493 0 0 1 8 12.5a4.49 4.49 0 0 1 1.544-3.393C9.077 9.038 8.564 9 8 9c-5 0-6 3-6 4Z" />
                                            <path
                                                d="M16 12.5a3.5 3.5 0 1 1-7 0 3.5 3.5 0 0 1 7 0Zm-3.5-2a.5.5 0 0 0-.5.5v1.5a.5.5 0 0 0 1 0V11a.5.5 0 0 0-.5-.5Zm0 4a.5.5 0 1 0 0-1 .5.5 0 0 0 0 1Z" />
                                        </svg>
                                    </td>
                                    <td @click="open_info_modal_confirmed(user._id)">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor"
                                            class=" blue bi bi-box-arrow-in-up-right" viewBox="0 0 16 16">
                                            <path fill-rule="evenodd"
                                                d="M6.364 13.5a.5.5 0 0 0 .5.5H13.5a1.5 1.5 0 0 0 1.5-1.5v-10A1.5 1.5 0 0 0 13.5 1h-10A1.5 1.5 0 0 0 2 2.5v6.636a.5.5 0 1 0 1 0V2.5a.5.5 0 0 1 .5-.5h10a.5.5 0 0 1 .5.5v10a.5.5 0 0 1-.5.5H6.864a.5.5 0 0 0-.5.5z" />
                                            <path fill-rule="evenodd"
                                                d="M11 5.5a.5.5 0 0 0-.5-.5h-5a.5.5 0 0 0 0 1h3.793l-8.147 8.146a.5.5 0 0 0 .708.708L10 6.707V10.5a.5.5 0 0 0 1 0v-5z" />
                                        </svg>
                                    </td>

                                </tr>

                            </tbody>
                        </table>
                    </div>
                </div>
                <div class="row">
                    <div class="col-12 col-lg-9">
                        <pagination @change-page="change_userlist_page" v-if="!check_for_filter()" />
                    </div>
                </div>

            </div>
        </div>
    </div>

    <div id="buttondiv" class="mini-button" :class="{ hideme: checkHideme }" style="color:green">
        <button @click="openCanvas" class="btn btn-success" type="button" aria-controls="offcanvasWithBothOptions"><svg
                xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-person-check"
                viewBox="0 0 16 16">
                <path
                    d="M12.5 16a3.5 3.5 0 1 0 0-7 3.5 3.5 0 0 0 0 7Zm1.679-4.493-1.335 2.226a.75.75 0 0 1-1.174.144l-.774-.773a.5.5 0 0 1 .708-.708l.547.548 1.17-1.951a.5.5 0 1 1 .858.514ZM11 5a3 3 0 1 1-6 0 3 3 0 0 1 6 0ZM8 7a2 2 0 1 0 0-4 2 2 0 0 0 0 4Z" />
                <path
                    d="M8.256 14a4.474 4.474 0 0 1-.229-1.004H3c.001-.246.154-.986.832-1.664C4.484 10.68 5.711 10 8 10c.26 0 .507.009.74.025.226-.341.496-.65.804-.918C9.077 9.038 8.564 9 8 9c-5 0-6 3-6 4s1 1 1 1h5.256Z" />
            </svg>
        </button>
    </div>
    <div class="offcanvas offcanvas-start" data-bs-scroll="true" tabindex="-1" id="offcanvasWithBothOptions"
        aria-labelledby="offcanvasWithBothOptionsLabel">
        <div class="offcanvas-header">
            <h5 class="offcanvas-title" id="offcanvasWithBothOptionsLabel">Deine ausgewählten Kontakte. Unten sind die
                möglichen Aktionen aufgelistet.</h5>
            <button @click="closeCanvas" type="button" class="btn-close text-reset" aria-label="Close"></button>
        </div>
        <div class="offcanvas-body">

            <a href="#" class="list-group-item list-group-item-action mt-3 list_item"
                v-for="sel_users in userlist.selected">
                <div :id="`listcontentdiv_${sel_users._id}`" class="selectedUser" @click="toggle_selected_user_state">
                    <div class="d-flex w-100 justify-content-between select_content_list_item">
                        <h5 class="mb-1">{{ sel_users.firstname }} {{ sel_users.lastname }},
                            {{
                                calculate_age(sel_users.birthdate) }} Jahre alt</h5>
                        <small class="text-muted">3 days ago</small>
                    </div>
                    <div class="d-flex flex-column w-100 justify-content-end">
                        <p class="mb-1">Registriert für:</p>
                        <div class="d-flex">
                            <small class="text-muted" v-for="jobs in sel_users.registeredjobs">{{ jobs }} </small>
                        </div>
                    </div>
                </div>
                <div :id="`listdecisiondiv_${sel_users._id}`" class="d-flex justify-content-between not-active">
                    <div class="btn-group ">
                        <button :id="`editmodebtn_${sel_users._id}`" class="btn btn-outline-info decision-btn-start"
                            @click="toggle_selected_user_state">
                            <svg xmlns="http://www.w3.org/2000/svg" width="30" height="30" fill="currentColor"
                                class="bi bi-box-arrow-left" viewBox="0 0 16 16">
                                <path fill-rule="evenodd"
                                    d="M6 12.5a.5.5 0 0 0 .5.5h8a.5.5 0 0 0 .5-.5v-9a.5.5 0 0 0-.5-.5h-8a.5.5 0 0 0-.5.5v2a.5.5 0 0 1-1 0v-2A1.5 1.5 0 0 1 6.5 2h8A1.5 1.5 0 0 1 16 3.5v9a1.5 1.5 0 0 1-1.5 1.5h-8A1.5 1.5 0 0 1 5 12.5v-2a.5.5 0 0 1 1 0v2z" />
                                <path fill-rule="evenodd"
                                    d="M.146 8.354a.5.5 0 0 1 0-.708l3-3a.5.5 0 1 1 .708.708L1.707 7.5H10.5a.5.5 0 0 1 0 1H1.707l2.147 2.146a.5.5 0 0 1-.708.708l-3-3z" />
                            </svg>
                        </button>
                        <button class="btn btn-danger decision_btn">Benutzer löschen</button>
                        <button class="btn btn-warning decision-btn-end">Von Auswahl entfernen</button>
                    </div>
                </div>
            </a>
        </div>

        <hr>
        <div class="btn-group  ">
            <button class="btn btn-outline-primary" @click="open_email_modal('selectedUsers')">Mail</button>
            <button @click="delete_users" class="btn btn-outline-primary">Löschen</button>
            <button @click="print_pdf" class="btn btn-outline-primary">PDF</button>
            <button @click="open_create_list_close_canvas" class="btn btn-outline-primary">Liste</button>
        </div>
        <hr>
    </div>
    <div class="modal" id="createlist-modal" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Eine Liste erstellen</h5>
                    <button type="button" class="btn-close" @click="close_create_list_modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <label for="listname" class="form-label">Bitte bennene die neue Liste</label>
                    <input type="text" placeholder="Köche Unter 25 Jahre..." class="form-control" id="listname"
                        v-model="listname_input">
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" @click="close_create_list_modal">Schließen</button>
                    <button @click="create_list" type="button" class="btn btn-primary" data-bs-dismiss="modal">Liste
                        erstellen</button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal" id="userinfo-modal" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Infos zu User</h5>
                    <button type="button" class="btn-close" @click="close_userinfo_modal_and_reset"
                        aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <!-- <div class="btn-group">
                        <button class="btn btn-outline-success" @click="change_activeInfoModalTab('umsetzer')"
                            v-if="currentUser.clicked.umsetzer">Umsetzer</button>
                        <button class="btn btn-outline-secondary" disabled v-else>Umsetzer</button>
                        <button class="btn btn-outline-success" @click="change_activeInfoModalTab('cook')"
                            v-if="currentUser.clicked.cook">Koch</button>
                        <button class="btn btn-outline-secondary" disabled v-else>Koch</button>
                        <button class="btn btn-outline-success" @click="change_activeInfoModalTab('komparse')"
                            v-if="currentUser.clicked.komparse">Komparse</button>
                        <button class="btn btn-outline-secondary" disabled v-else>Komparse</button>
                        <button class="btn btn-outline-success" @click="change_activeInfoModalTab('service')"
                            v-if="currentUser.clicked.service">Servicekraft</button>
                        <button class="btn btn-outline-secondary" disabled v-else>Servicekraft</button>
                    </div> -->
                    <!-- <hr class="my-3"> -->
                    <div class="generalinfo">

                        <div class="row justify-content-between">
                            <div class="col-6">
                                <img :src="unpack(currentUser.clicked.picture1)"
                                    style="max-width:150px; max-height:200px;">
                            </div>
                            <div class="col-6">
                                <img :src="unpack(currentUser.clicked.picture2)"
                                    style="max-width:150px; max-height:200px;">
                            </div>
                            <hr class="my-2">
                        </div>
                        <div class="row justify-content-between">
                            <div class="col-10">
                                <p class="infomodal"> Name: {{ currentUser.clicked.firstname }} {{
                                    currentUser.clicked.lastname }}</p>
                                <p class="infomodal">Geburtstag: {{
                                    calculate_age(currentUser.clicked.birthdate) }}</p>
                                <p class="infomodal"> Email: {{ decode_3times(currentUser.clicked.email) }}</p>
                                <p class="infomodal"> Telefon: {{ currentUser.clicked.phone }}</p>

                            </div>
                        </div>
                        <div class="row justify-content-between">
                            <div class="col-10">
                                <p class="infomodal">Körpergröße: {{ currentUser.clicked.size }}</p>
                                <p class="infomodal">Kleidergröße: {{ currentUser.clicked.cloth_size }}</p>
                            </div>
                        </div>
                    </div>


                    <!-- <div v-if="activeInfoModalTab == 'cook'" class="cook">
                        <hr class="my-2">
                        <h5>Downloade Ausbildungs Nachweis</h5>
                        <button class="btn btn-outline-primary" style="margin-right: 10px; margin-left:20px;"
                            @click="downloadPDF(currentUser.clicked.cook_confirmation_pdf_req)">Download PDF
                            (required)</button>
                        <button class="btn btn-outline-primary"
                            @click="downloadPDF(currentUser.clicked.cook_confirmation_pdf_opt)">Download PDF
                            (optional)</button>
                    </div> -->
                    <!-- <div v-if="activeInfoModalTab == 'komparse'" class='komparse'>
                        <hr class="my-2">
                        <div class="row justify-content-between">
                            <div class="col-6">
                                <img :src="currentUser.clicked.komparse_pic1" style="max-width:200px; max-height:200px;">
                            </div>
                            <div class="col-6">
                                <img :src="currentUser.clicked.komparse_pic2" style="max-width:200px; max-height:200px;">
                            </div>
                        </div>
                        
                    </div> -->
                    <!-- <div v-if="activeInfoModalTab == 'service'" class='service'>
                        <hr class="my-2">
                    </div>
                    <div v-if="activeInfoModalTab == 'umsetzer'" class='umsetzer'>
                        <hr class="my-2">
                        <div class="row justify-content-between">
                            <div class="col-6">
                                <img :src="currentUser.clicked.umsetzer_driversLicense_front"
                                    style="max-width:200px; max-height:200px;">
                            </div>
                            <div class="col-6">
                                <img :src="currentUser.clicked.umsetzer_driversLicense_back"
                                    style="max-width:200px; max-height:200px;">
                            </div>
                        </div>
                    </div> -->


                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary"
                        @click="close_userinfo_modal_and_reset">Schließen</button>
                    <button @click="confirm_user" type="button" class="btn btn-primary" id="userconfirm_btn"
                        data-bs-dismiss="modal">User
                        bestätigen
                    </button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal" ref="filtermodal" id="filter-modal" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Filter die Benutzer einträge</h5>
                    <button type="button" class="btn-close" @click="close_filter_modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <label for="job_select" class="form-label">Wähle aus welche Benutzer, mit welchem Job angeziegt werden
                        sollen.</label>
                    <div class="btn-group">
                        <div class="container">
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Geschlecht auswählen
                                </div>
                                <div class="col-6">
                                    <select class="form-select" aria-label="Default select example" name="gender"
                                        id="gender">
                                        <option value="" selected="">Alle</option>
                                        <option value="male">Männer</option>
                                        <option value="female">Frauen</option>
                                    </select>
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Impfstatus auswählen
                                </div>
                                <div class="col-6">
                                    <select class="form-select" aria-label="Default select example" name="vaccinated"
                                        id="vaccinated">
                                        <option value="" selected="">Egal</option>
                                        <option value="geimpft">Geimpft</option>
                                        <option value="nicht_geimpft">Ungeimpft</option>
                                    </select>
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-6">
                                    <p class="fs-4">Alter</p>
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Alle über X Jahre
                                </div>
                                <div class="col-6">
                                    <input type="number" placeholder="Alter eingabe" class="form-control" name="min_age"
                                        id="min_age" min="5" max="100">
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Alle unter X Jahre
                                </div>
                                <div class="col-6">
                                    <input type="number" placeholder="Alter eingabe" class="form-control" name="max_age"
                                        id="max_age" min="5" max="100">
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-6">
                                    <p class="fs-4">Kleidergröße</p>
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Alle über Kleidergröße X
                                </div>
                                <div class="col-6">
                                    <select class="form-select" aria-label="Default select example" name="min_cloth_size"
                                        id="min_cloth_size">
                                        <option value="" selected="">Alle</option>
                                        <option value="XS">XS</option>
                                        <option value="S">S</option>
                                        <option value="M">M</option>
                                        <option value="L">L</option>
                                        <option value="XL">XL</option>
                                    </select>
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Alle unter Kleidergröße X
                                </div>
                                <div class="col-6">
                                    <select class="form-select" aria-label="Default select example" name="max_cloth_size"
                                        id="max_cloth_size">
                                        <option value="" selected="">Alle</option>
                                        <option value="XS">XS</option>
                                        <option value="S">S</option>
                                        <option value="M">M</option>
                                        <option value="L">L</option>
                                        <option value="XL">XL</option>
                                    </select>
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-6">
                                    <p class="fs-4">Körpergröße</p>
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Alle über X cm
                                </div>
                                <div class="col-6">
                                    <input type="number" placeholder="min. Körpergröße eingeben" class="form-control"
                                        name="min_size" id="min_size" min="50" max="250">
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Alle unter X cm
                                </div>
                                <div class="col-6">
                                    <input type="number" placeholder="max. Körpergröße eingeben" class="form-control"
                                        name="max_size" id="max_size" min="50" max="250">
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-12">
                                    <p class="fs-4">Ort</p>
                                    <p>Wenn PLZ eingegeben wird, wird nach PLZ gesucht. Wenn beides auch nach PLZ.</p>
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Postleitzahl
                                </div>
                                <div class="col-6">
                                    <input type="number" placeholder="87435" class="form-control" name="zip" id="zip">
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Stadt
                                </div>
                                <div class="col-6">
                                    <input type="text" placeholder="Kempten" class="form-control" name="city" id="city">
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-12">
                                    <p class="fs-4">Infos</p>
                                    <p>Suche hiermit nach den Zusatzinformationen, z.b. Hund.</p>
                                </div>
                            </div>
                            <div class="row p-2">
                                <div class="col-6 p-2">
                                    Durchsuche nach:
                                </div>
                                <div class="col-6">
                                    <input type="text" placeholder="hund" class="form-control" name="extra_infos"
                                        id="extra_infos">
                                </div>
                            </div>
                        </div>
                    </div>
                    <!-- I can select each job, changes the button or whatever to "Selected" and filter_table function will look for every "selected button" and search for the jobs.-->
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" @click="close_filter_modal">Schließen</button>
                    <button @click="use_filter()" type="button" class="btn btn-primary" data-bs-dismiss="modal">Filter
                        anwenden</button>
                </div>
            </div>
        </div>
    </div>
    <div class="modal" id="email-modal" tabindex="-1">
        <div class="modal-dialog modal-dialog-centered modal-dialog-scrollable">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title">Filter die Benutzer einträge</h5>
                    <button type="button" class="btn-close" @click="close_email_modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <label for="froala-editor" class="form-label">Wähle aus welche Benutzer, mit welchem Job angeziegt
                        werden
                        sollen.</label>
                    <!-- <textarea id="froala-editor">Initialize the Froala WYSIWYG HTML Editor on a textarea.</textarea> -->
                    <QuillEditor ref="quill_richtext" v-model:content="content_quillEditor" />
                    <!-- I can select each job, changes the button or whatever to "Selected" and filter_table function will look for every "selected button" and search for the jobs.-->
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" @click="close_email_modal">Schließen</button>
                    <button @click="send_email" type="button" class="btn btn-primary" data-bs-dismiss="modal">Email
                        Senden</button>
                </div>
            </div>
        </div>
    </div>
</template>

    

<script setup>
import { computeStyles, offset } from '@popperjs/core';
import pagination from '../pageparts/pagination.vue';
import { defineProps, ref, onMounted, reactive, defineEmits, inject, toRef, toRefs, nextTick, watch } from 'vue'
// import { bootstrap } from 'bootstrap';
import { Modal, Offcanvas } from 'bootstrap'
import { QuillEditor } from '@vueup/vue-quill'

import '@vueup/vue-quill/dist/vue-quill.snow.css'


let richtexteditor_content = ref("");
let quill_richtext = ref(null);
let content_quillEditor = ref('');
let newContent = ref('');
let emails_for_send_email = ref([]);
watch(content_quillEditor, newValue => {
    newContent.value = newValue;
})
// VueFroala.register();
const props = defineProps(["navbar_space", "content_space", "isMobil", "filterArray"]);
const emits = defineEmits(["print-pdf", "get-user-by-id", 'toggle-list-icon', 'set-filter', "get-filter", "set-filterval", "reset-filter"]);
let currentSort = ref("lastname");
let currentSortDir = ref("asc");
let isMobil = toRef(props, "isMobil");
console.log(isMobil);
let navbarspace = ref(props.navbar_space);
let nothingOpen = ref(true);
let contentspace = ref(props.content_space);
let listname_input = ref("");
let filter = ref(props.filterArray);
let currentuser_cook = ref(false);
let currentuser_umsetzer = ref(false);
let currentuser_service = ref(false);
let currentuser_komparse = ref(false);
let filterSelect = [];
let checkHideme = ref(false);
let activeInfoModalTab = ref("info");
let userlist = reactive({
    users: [],
    headers: [],
    selected: []
});
let currentUser = reactive({
    clicked: false,


});
let modals = reactive({
    userinfo: null,
    offcanvas: null,
    createList: null,
    filter: null,
    email: null
});
let filtermodal = ref(null);
onMounted(() => {
    console.log("mounted");
    fetch_users("1", "50");

    modals.userinfo = new Modal('#userinfo-modal', {});
    modals.offcanvas = new Offcanvas('#offcanvasWithBothOptions', {});
    modals.createList = new Modal('#createlist-modal', {});
    modals.filter = new Modal('#filter-modal', {});
    modals.email = new Modal('#email-modal', {});
    if (document.getElementById("sidebarMenu").classList.contains("show")) {
        checkHideme.value = true;
    }


});

function print_pdf() {
    emits('print-pdf', userlist.selected);
}

async function fetch_users(page, limit) {

    console.log(filter.value);

    let filterObj = { ...filter.value };
    console.log(filterObj);
    console.log(filterObj.value);
    //add page and limit to filterObj
    filterObj.page = page;
    filterObj.limit = limit;
    console.log(filterObj);
        fetch("https://api.allgaeu-komparsen.de/users/get_users", {

            method: "POST",
            body: JSON.stringify(filterObj),
            headers: {
                "Content-Type": "application/json"
            }

        })
            .then(response => response.json())
            .then(data => {
                console.log(data);
                userlist.users = [];
                for (let i = 0; i < data.length; i++) {
                    data[i].email = decodeURIComponent(data[i].email);
                    userlist.users.push((data[i]));


                }
                console.log(userlist);

            })
            .catch(error => {
                console.error(error);
            });
    
    // fetch("http://localhost:5174/users/get_users/" + page + "/" + limit, {

}
function toggle_selected_user_state(event) {
    //element == a tag
    let id = event.currentTarget.id;
    console.log(event.currentTarget.id);
    //split id at _ 
    let id_arr = id.split("_");
    let user_id = id_arr[1];
    let decision_ele = document.getElementById("listdecisiondiv_" + user_id);
    let content_ele = document.getElementById("listcontentdiv_" + user_id);



    if (decision_ele.classList.contains("not-active")) {
        decision_ele.classList.remove("not-active");
        content_ele.classList.add("not-active");

    }
    else {
        decision_ele.classList.add("not-active");
        content_ele.classList.remove("not-active");
    }
    //split id on _#

}

// methode schreiben, die unconfirmed zuerst zeigt.
// dann confirmed
//insgesamt aber nur limit viele. 
function calculate_age(birthdate) {
    let today = new Date();
    let birthDate = new Date(birthdate);
    let age = today.getFullYear() - birthDate.getFullYear();
    let m = today.getMonth() - birthDate.getMonth();
    if (m < 0 || (m === 0 && today.getDate() < birthDate.getDate())) {
        age--;
    }

    return age;
}
function toggle_checkbox(index) {
    console.log("toggle checkbox start");
    console.log(userlist.selected);
    if (document.getElementById("checkbox_" + index).checked) {
        document.getElementById("checkbox_" + index).checked = false;
        console.log("checked = t rue");
        userlist.selected.splice(userlist.selected.indexOf(userlist.users[index]), 1);


    }
    else {
        console.log("checked = false");
        document.getElementById("checkbox_" + index).checked = true;
        userlist.selected.push(userlist.users[index]);
        // console.log(userlist.selected);
    }
    console.log("toggle checkbox end");
    console.log(userlist.selected);

}
//unterschied: beide funktionen speichern die zugehörige value in das sidebar array.
//aber benutzt man die obere funktion für die checkboxen werden die wieder autoamtisch auf false gesetzt. 
//wird eine checkbox gechecked, wird .checked == true  --- bei der funktion wird aber dann geprüft ob diese checked ist.
function toggle_checkbox_inputfield(index) {
    console.log("toggle checkbox inputfield start");
    console.log(userlist.selected);
    if (document.getElementById("checkbox_" + index).checked) {
        userlist.selected.push(userlist.users[index]);
    }
    else {
        userlist.selected.splice(userlist.selected.indexOf(userlist.users[index]), 1);
    }
    console.log("toggle checkbox inputfield end");
    console.log(userlist.selected);
}





function sort_users_str(sortby) {
    if (sortby == currentSort.value) {
        if (currentSortDir.value == "asc") {
            currentSortDir.value = "desc";
        }
        else {
            currentSortDir.value = "asc";
        }

    }
    else {
        if (currentSortDir.value == "desc") {
            currentSortDir.value = "asc";
        }
        else {
            currentSortDir.value = "desc";
        }
    }
    currentSort.value = sortby;

    if (currentSortDir.value == "asc") {
        userlist.users.sort((a, b) => {
            if (a[sortby].toLowerCase() < b[sortby].toLowerCase()) {
                return -1;
            }
            if (a[sortby].toLowerCase() > b[sortby].toLowerCase()) {
                return 1;
            }
            return 0;
        });
    }
    else {
        userlist.users.sort((a, b) => {
            if (a[sortby].toLowerCase() > b[sortby].toLowerCase()) {
                return -1;
            }
            if (a[sortby].toLowerCase() < b[sortby].toLowerCase()) {
                return 1;
            }
            return 0;
        });
    }

}

function sort_users_num(sortby) {
    if (sortby == currentSort.value) {
        if (currentSortDir.value == "asc") {
            currentSortDir.value = "desc";
        }
        else {
            currentSortDir.value = "asc";
        }

    }
    else {
        if (currentSortDir.value == "desc") {
            currentSortDir.value = "asc";
        }
        else {
            currentSortDir.value = "desc";
        }
    }
    currentSort.value = sortby;
    if (sortby == "age") {
        userlist.users.sort(compare_users);
    }

}
function compare_users(a, b) {
    if (currentSortDir.value == "desc") {
        return b - a;
    }
    else {
        return a - b;

    }
}
function change_userlist_page(page, limit) {
    fetch_users(page, limit);

}

function create_list() {
    let tmp_arr = [];
    let listname = listname_input.value;
    console.log(userlist.selected);
    for (let i = 0; i < userlist.selected.length; i++) {
        tmp_arr.push(userlist.selected[i]._id);
    }
    fetch("https://api.allgaeu-komparsen.de/userlist/", {
        // fetch("http://localhost:5174/userlist/", {
        method: "POST",
        headers: { users: JSON.stringify(tmp_arr), listname: listname }
    })
        .then(response => response.json())
        .then(data => {


        })
        .catch(error => {
            console.error(error);
        });
}
async function open_info_modal(userid) {
    try {
        console.log('moin');
        let user = await fetch_user_by_id(userid);
        currentUser.clicked = user;
        //get the modal userinfo-modal and open it 

        console.log(user);
        if (user.komparse) {
            currentuser_komparse.value = true;
        }
        if (user.umsetzer) {
            currentuser_umsetzer.value = true;
        }
        if (user.cook) {
            currentuser_cook.value = true;
        }
        if (user.service) {
            currentuser_service.value = true;
        }
        open_userinfo_modal();
        console.log(currentUser.clicked);
    } catch (error) {
        console.error(error);
        // Handle the error appropriately
    }
}
async function fetch_user_by_id(id) {
    try {
        let response = await fetch("https://api.allgaeu-komparsen.de/users/get/" + id, {
            // let response = await fetch("http://localhost:5174/users/get/" + id, {
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
function close_userinfo_modal_and_reset() {

    currentuser_komparse.value = false;
    currentuser_umsetzer.value = false;
    currentuser_cook.value = false;
    currentuser_service.value = false;
    close_userinfo_modal();
}
function confirm_user() {
    let id = currentUser.clicked._id;
    try {
        // let response = fetch("http://localhost:5174/users/confirm/" + id, {
        let response = fetch("https://api.allgaeu-komparsen.de/users/confirm/" + id, {
            method: "PUT",
            //headers: {limit : limit.value , page : page.value}
        });


    }
    catch (error) {
        console.error(error);
        // Handle the error appropriately

    }
}


function delete_users() {
    let selectedUsers = document.getElementsByClassName("selectedUser");
    //loop through all of them and concat the id in a string id1,id2,id3
    let ids = "";
    for (let i = 0; i < selectedUsers.length; i++) {
        //before adding the id remove the front part of id which is _ 
        ids += selectedUsers[i].id.split("_")[1] + ",";

    }
    //remove the last comma
    ids = ids.substring(0, ids.length - 1);
    try {
        let response = fetch("https://api.allgaeu-komparsen.de/users/delete/" + ids, {
            // let response = fetch("http://localhost:5174/users/delete/" + ids, {
            method: "DELETE",
            //headers: {limit : limit.value , page : page.value}
        });


    }
    catch (error) {
        console.error(error);
        // Handle the error appropriately

    }
}


function closeCanvas() {
    modals.offcanvas.hide();
    nothingOpen.value = true;
}
function openCanvas() {
    if (nothingOpen.value) {
        modals.offcanvas.show();
        nothingOpen.value = false;
    }
}
function open_userinfo_modal() {
    if (nothingOpen.value) {
        modals.userinfo.show();
        nothingOpen.value = false;
    }
    console.log("userinfomodal");
    console.log(currentUser.clicked);
}
function close_userinfo_modal() {
    modals.userinfo.hide();
    nothingOpen.value = true;
    activeInfoModalTab.value = "info";
}
function open_filter_modal() {
    if (nothingOpen.value) {
        resetFilter();
        modals.filter.show();
        nothingOpen.value = false;
    }
}
function close_filter_modal() {
    modals.filter.hide();
    nothingOpen.value = true;

}
function open_create_list_modal() {
    if (nothingOpen.value) {
        modals.createList.show();
        nothingOpen.value = false;
    }
}
function close_create_list_modal() {
    modals.createList.hide();
    nothingOpen.value = true;
}
async function open_info_modal_confirmed(userid) {
    let user = await fetch_user_by_id(userid);
    currentUser.clicked = user;
    console.log(currentUser.clicked);
    open_userinfo_modal();
    document.getElementById("userconfirm_btn").classList.add("hideme");
}
function open_create_list_close_canvas() {
    closeCanvas();
    open_create_list_modal();
}
function close_email_modal() {
    modals.email.hide();
    nothingOpen.value = true;
}
function open_email_modal(type, id = "") {
    closeCanvas();
    if (nothingOpen.value) {
        modals.email.show();
        console.log(modals.email);
        nothingOpen.value = false;
    }
    if (type == "EmailById") {
        emails_for_send_email.value.push(id);
    }
    else if (type == "selectedUsers") {
        console.log(userlist.selected);
        for (let i = 0; i < userlist.selected.length; i++) {
            emails_for_send_email.value.push(userlist.selected[i].email);
        }
    }


}
function toggle_jobfilter(job) {
    let foundInFilter = false;
    let htmlEle = document.getElementById(job + "_jobfilterbtn");
    for (let i = 0; i < filterSelect.length; i++) {
        if (filterSelect[i] == job) {
            //means filter is already in Array. so remove it and change button to outline
            filterSelect.splice(i, 1);
            foundInFilter = true;
            htmlEle.classList.remove("btn-primary");
            htmlEle.classList.add("btn-outline-primary");
            break;
        }


    }
    if (!foundInFilter) {
        filterSelect.push(job);
        htmlEle.classList.remove("btn-outline-primary");
        htmlEle.classList.add("btn-primary");
        //change button to primary
    }



}
function resetFilter() {
    nothingOpen.value = true;
    emits("reset-filter");

}

function fitler_table() {

    //reset Table, make new table. 
    userlist.users = [];
    //filterselect is an array, make it a string with , seperator
    let filterSelectString = "";
    for (let i = 0; i < filterSelect.length; i++) {
        filterSelectString += filterSelect[i] + ",";
    }

    // userlist_type.value = filterSelectString;
    fetch_filtered_users();
    nothingOpen.value = true;

}
async function fetch_filtered_users() {
    let tmp_arr = [];

    await fetch("https://api.allgaeu-komparsen.de/users/filtered", {
        // await fetch("http://localhost:5174/users/filtered", {
        method: "GET",
        headers: { jobs: JSON.stringify(filterSelect) }
    })
        .then(response => response.json())
        .then(data => {
            userlist.users = [];
            for (let i = 0; i < data.length; i++) {
                data[i].email = decodeURIComponent(data[i].email);
                userlist.users.push((data[i]));


            }

        })
        .catch(error => {
            console.error(error);
        });
}
function check_for_filter() {
    // console.log(filterSelect);
    if (filterSelect.length > 0) {
        // console.log("fiulter is not empty");
        return true;
    }
    else {
        return false;
    }
}
function check_for_confirmed(user) {
    // console.log(user.user_confirmed_at);
    if (user.user_confirmed_at != null) {
        // console.log("user is confirmed");
        return true;
    }
    else {
        return false;
    }
}

function decode_3times(string) {
    return decodeURIComponent(decodeURIComponent(decodeURIComponent(string)));
}
function change_activeInfoModalTab(newval) {
    activeInfoModalTab.value = newval;
}
async function downloadPDF(base64string) {

    // Convert the base64 string to a Blob object
    console.log(base64string);

    const arrayBuffer = await fetch(`${base64string}`).then((response) =>
        response.arrayBuffer()
    );

    const blob = new Blob([arrayBuffer], { type: 'application/pdf' });
    const url = URL.createObjectURL(blob);

    const link = document.createElement('a');
    link.href = url;
    link.download = 'file.pdf';
    link.click();

    URL.revokeObjectURL(url);
}


function send_email() {
    // send_to = "hendrik.wilms97@gmail.com";
    // subject = "test";
    // body = "test email body";
    //fetch user/send_email
    nextTick(() => {
        console.log();
    });
    console.log(newContent);
    //get email adresses from selected users. 
    //or get email adress by id
    let tmpemail_addresses = emails_for_send_email.value;

    // console.log(quill_richtext.value.root.innterHtml);
    console.log(tmpemail_addresses);
    // let text_for_email = newContent.value;
    let somethingelse = "";
    if (quill_richtext.value && quill_richtext.value.editor) {
        let quill = quill_richtext.value.editor;
        somethingelse = quill.innerHTML;
        // let somethign = quill;
        // let delta = quill.getContents();
        // let html = quill_richtext.value.clipboard.convert(delta);
        // console.log(html); // Here you have the HTML string
    }
    fetch("https://api.allgaeu-komparsen.de/users/send_email", {
        //   fetch("http://localhost:5174/users/send_email", {
        method: "POST",
        headers: {
            'Content-Type': 'application/json',
            'Email_address': tmpemail_addresses,
            'Subject': "Info Email von Allgäu Komparsen",
            'From': "info@allgaeu-komparsen.de",
            'Text': JSON.stringify(somethingelse)
        }

        // headers: { Email_address: tmpemail_addresses, Subject: "test", Text: ops, From: "info@helu-design.de" }
    })
        .then(response => response.json())
        .then(data => {
            console.log(data);
        })
        .catch(error => {
            console.error(error);
        });
    emails_for_send_email.value = [];
}
function use_filter() {

    let max_size = filtermodal.value.querySelector("#max_size").value;
    let min_size = filtermodal.value.querySelector("#min_size").value;
    let min_cloth_size = filtermodal.value.querySelector("#min_cloth_size").value;
    let max_cloth_size = filtermodal.value.querySelector("#max_cloth_size").value;
    let gender = filtermodal.value.querySelector("#gender").value;
    let min_age = filtermodal.value.querySelector("#min_age").value;
    let max_age = filtermodal.value.querySelector("#max_age").value;
    let city = filtermodal.value.querySelector("#city").value;
    let zip = filtermodal.value.querySelector("#zip").value;
    console.log(filtermodal.value.querySelector("#vaccinated"));
    let vaccinated = filtermodal.value.querySelector("#vaccinated").value;
    let extra_infos = filtermodal.value.querySelector("#extra_infos").value;

    //check if each variable has a value, if they have add them to a key value array
    if (max_size != "") {
        console.log("max_size");
        set_filter_value("max_size", max_size);
    }
    if (min_size != "") {
        console.log("min_size");
        set_filter_value("min_size", min_size);

    }
    if (min_cloth_size != "") {
        console.log("min_cloth_size");
        set_filter_value("min_cloth_size", min_cloth_size);

    }
    if (max_cloth_size != "") {
        console.log("max_cloth_size");
        set_filter_value("max_cloth_size", max_cloth_size);
    }
    if (gender != "") {
        console.log("gender");
        set_filter_value("gender", gender);
    }
    if (min_age != "") {
        console.log("min_age");
        set_filter_value("min_age", min_age);
    }
    if (max_age != "") {
        console.log("max_age");
        set_filter_value("max_age", max_age);
    }
    if (city != "") {
        console.log("city");
        set_filter_value("city", city);
    }
    if (zip != "") {
        console.log("zip");
        set_filter_value("zip", zip);
    }
    if (vaccinated != "") {
        console.log("vaccinated");
        set_filter_value("vaccinated", vaccinated);
    }
    if (extra_infos != "") {
        console.log("extra infos");
        set_filter_value("extra_infos", extra_infos);
    }
    console.log(filter.value);
    close_filter_modal();
    fetch_users("1", "10");
}
function set_filter(filter) {
    emits("set-filter", filter);
}
function set_filter_value(key, value) {
    emits("set-filterval", key, value);
}
function get_filter() {
    let bla = emits("get-filter");
    console.log(bla);
    return bla;
}
function unpack(picture){
    console.log(picture);

    if(picture == -1 || picture == undefined){
        console.log("-1");
        return "";
    }
    else {
        console.log("not 1");
        return "data:image/jpeg;base64," + decodeURIComponent(picture.byte);
    }
}
</script>