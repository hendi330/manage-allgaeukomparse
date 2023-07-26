<template>
    <div class="container-fluid">
        <div class="row">
            <div :class="[navbarspace]">

            </div>
            <div :class="[contentspace]">
                <div class="row">
                    <div class="col-md-12">
                        <h1>Profil</h1>
                    </div>
                </div>
                <div class="row ">
                    <h4>Lass hier deine QR Codes generieren. Jeder Button generiert für die entsprechende Webseite einen
                        QR-Code</h4>

                    <div class="col-12 col-md-4">
                        <div class="input-group mb-3">
                            <input type="text" class="form-control" placeholder="Recipient's username"
                                aria-label="Recipient's username" aria-describedby="button-addon2" v-model="qrcodeval">
                            <button @click="generateQRCode(qrcodeval)" class="btn btn-outline-secondary" type="button"
                                id="button-addon2">Generiere QR-Code</button>
                        </div>
                        <p><b style="color:red"> <u>WICHTIG: </u></b> Kein HTTPS --> <b
                                style="color:green">www.streetkitchen.de </b> | <s
                                style="color:orange">https://streetkitchen.de</s></p>
                    </div>
                </div>
                <div class="row">
                    <div class="col-8">
                        <h4>QR Code</h4>
                        <h5 id="description">URL: register</h5>
                        <img alt="QR Code" id="qrCode">
                    </div>

                </div>
                <div class="col-4">
                    <button @click="downloadQRCode('pdf')" class="btn btn-primary">Download QR Code (PDF)</button>
                    <button @click="downloadQRCode('psdf')" class="btn btn-primary">Download QR Code (PNG)</button>

                </div>
                <hr class="my-4">
                <div class="row">
                    <div class="col-6">
                        <button @click="generate_hash_code()" class="btn btn-primary">Link zum Registrieren generieren</button>
                    </div>
                </div>
                <div class="row">
                    <div class="col-lg-3">
                        <p style="margin-top:10px;" id="Link">Hello</p>
                    </div>
                   
                </div>
                <div class="row">
                    <div class="col-6">
                        <button @click ="copy_link()" class="btn btn-primary">Link in Zwischenablage kopieren</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
<script setup>
import { defineProps, ref } from 'vue'
import { jsPDF } from "jspdf";
const props = defineProps(["navbar_space", "content_space"]);
let navbarspace = ref(props.navbar_space);
let contentspace = ref(props.content_space)
let qrcodeval = ref('');
function generateQRCode(urlInput) {
    let url = urlInput;
    fetch("http://localhost:5174/users/qrcode/" + url)
        //.then returns a base64string 
        .then(response => response.json())
        .then(data => {
            console.log(data);
            let qrCode = document.getElementById("qrCode");
            qrCode.src = "data:image/png;base64," + data;
            let qrCodeDescription = document.getElementById("description");
            qrCodeDescription.innerHTML = "URL: " + url;
        })
        .catch(error => console.log(error));
}
function downloadQRCode(type) {
    if (type == "pdf") {
        //get the qr code from the img tag, img tag holds base64 string as src 
        let qrCode = document.getElementById("qrCode");
        //create a pdf document
        let pdf = new jsPDF();
        //add the qr code to the pdf document
        pdf.addImage(qrCode.src, "PNG", 0, 0);
        //save the pdf document
        pdf.save("qrCode.pdf");

    }
    else {
        ////get the qr code from the img tag, img tag holds base64 string as src and download the qrcide as png
        let qrCode = document.getElementById("qrCode");
        let a = document.createElement("a");
        a.href = qrCode.src;
        a.download = "qrCode.png";
        a.click();

    }
}

function generate_hash_code() {
    fetch("http://localhost:5174/hash/generate", {
        method: "POST",

    })
        .then(response => response.json())
        .then(data => {
            console.log(data);
            // let hash = data["hash"];
            // let url = "http://localhost:5174/";
            let url = "https://register.allgaeu-komparse.de/";
            document.getElementById("Link").innerHTML = url+data;


        })
        .catch(error => console.log(error));


}
function copy_link(){
    //copy the link to the clipboard
    let link = document.getElementById("Link");
    let range = document.createRange();
    range.selectNode(link);
    window.getSelection().removeAllRanges();
    window.getSelection().addRange(range);
    document.execCommand("copy");
    window.getSelection().removeAllRanges();
    //change the text of the link

}   
</script>