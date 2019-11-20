<template>
 <v-container>
 <v-card>
 <v-container grid-list-md mb-0>
 <h2 class="text-md-center">Data Kendaraan</h2>
 <v-layout row wrap style="margin:10px">
 <v-flex xs6>
 <v-btn
 depressed
 dark
 rounded
 style="text-transform: none !important;"
 color = "green accent-3"
 @click="dialog = true"
 >
 <v-icon size="18" class="mr-2">mdi-pencil-plus</v-icon>
 Tambah Kendaraan
 </v-btn>
 </v-flex>
 <v-flex xs6 class="text-right">
 <v-text-field
 v-model="keyword"
 append-icon="mdi-search"
 label="Search"
 hide-details
 ></v-text-field>
 </v-flex>
 </v-layout>
 <v-data-table
 :headers="headers"
 :items="kendaraans"
 :search="keyword"
 :loading="load"
 >
 <template v-slot:body="{ items }">
 <tbody>
 <tr v-for="(item,index) in items" :key="item.id">
 <td>{{ index + 1 }}</td>
 <td>{{ item.merk }}</td>
 <td>{{ item.type}}</td>
 <td>{{ item.licensePlate }}</td>
 <td>{{ item.created_at }}</td>
 <td class="text-center">
 <v-btn
 icon
color="indigo"
light
@click="editHandler(item)"
 >
 <v-icon>mdi-pencil</v-icon>
 </v-btn>
 <v-btn
 icon
color="error"
light
@click="deleteData(item.id)"
 >
 <v-icon>mdi-delete</v-icon>
 </v-btn>
 </td>
 </tr>
 </tbody>
 </template>
 </v-data-table>
 </v-container>
 </v-card>
 <v-dialog v-model="dialog" persistent max-width="600px">
 <v-card>
 <v-card-title>
 <span class="headline">Data Kendaraan</span>
 </v-card-title>
 <v-card-text>
 <v-container>
 <v-row>
 <v-col cols="12">
 <v-text-field label="Merk*" v-model="form.merk" required></v-text-field>
 </v-col>
 <v-col cols="12">
     <v-select :items="items" label="Type*" v-model="form.type" required></v-select>
 </v-col>
 <v-col cols="12">
        <v-text-field label="Lisence Plate*" v-model="form.licensePlate" required></v-text-field>
 </v-col>
 </v-row>
 </v-container>
 <small>*indicates required field</small>
 </v-card-text>
 <v-card-actions>
 <v-spacer></v-spacer>
 <v-btn color="blue darken-1" text @click="dialog = false">Close</v-btn>
 <v-btn color="blue darken-1" text @click="setForm()">Save</v-btn>
 </v-card-actions>
 </v-card>
 </v-dialog>
 <v-snackbar
 v-model="snackbar"
 :color="color"
 :multi-line="true"
 :timeout="3000"
 >
 {{ text }}
 <v-btn
 dark
 text
 @click="snackbar = false"
 >
 Close
 </v-btn>
 </v-snackbar>
 </v-container>
</template>
<script>
export default {
 data () {
     
 return {
 dialog: false,
 keyword: '',
 headers: [
 {
 text: 'No',
 value: 'no',
 },
 {
 text: 'Merk',
 value: 'merk'
 },
 {
 text: 'Type',
 value: 'type'
 },
 {
 text: 'LicensePlate',
 value: 'licensePlate'
 },
 {
 text: 'created_at',
 value: 'created_at'
 },
 {
 text: 'Aksi',
 value: null
 },
 ],
 items: ['Mobil','Motor'],
 kendaraans: [],
 snackbar: false,
 color: null,
 text: '',
 load: false,
 form: {
 merk : '',
 type : '',
 licensePlate : ''
 },
 kendaraan : new FormData,
 typeInput: 'new',
 errors : '',
 updatedId : '',
 }
 },
 methods:{
 getData(){
 var uri = this.$apiUrl + '/kendaraan'
 this.$http.get(uri).then(response =>{
 this.kendaraans=response.data.message
 })
 },
 sendData(){
 this.kendaraan.append('merk', this.form.merk);
 this.kendaraan.append('type', this.form.type);
 this.kendaraan.append('licensePlate', this.form.licensePlate);
 var uri =this.$apiUrl + '/kendaraan'
 this.load = true
 this.$http.post(uri,this.kendaraan).then(response =>{
 this.snackbar = true; //mengaktifkan snackbar
 this.color = 'green'; //memberi warna snackbar
 this.text = response.data.message; //memasukkan pesan ke snackbar
 this.load = false;
 this.dialog = false
 this.getData(); //mengambil data user
 this.resetForm();
 }).catch(error =>{
 this.errors = error
 this.snackbar = true;
 this.text = 'Try Again';
 this.color = 'red';
 this.load = false;
 })
 },
 updateData(){
 this.kendaraan.append('merk', this.form.merk);
 this.kendaraan.append('type', this.form.type);
 this.kendaraan.append('licensePlate', this.form.licensePlate);
 var uri = this.$apiUrl + '/kendaraan/' + this.updatedId;
 this.load = true
 this.$http.post(uri,this.kendaraan).then(response =>{
 this.snackbar = true; //mengaktifkan snackbar
 this.color = 'green'; //memberi warna snackbar
 this.text = response.data.message; //memasukkan pesan ke snackbar
 this.load = false;
 this.dialog = false
 this.getData(); //mengambil data user
 this.resetForm();
 this.typeInput = 'new';
 }).catch(error =>{
 this.errors = error
 this.snackbar = true;
 this.text = 'Try Again';
 this.color = 'red';
 this.load = false;
 this.typeInput = 'new';
 })
 },
 editHandler(item){
 this.typeInput = 'edit';
 this.dialog = true;
 this.form.merk = item.merk;
 this.form.type = item.type;
 this.form.licensePlate = item.licensePlate,
 this.updatedId = item.id
 },
 deleteData(deleteId){ //mengahapus data
 var uri = this.$apiUrl
+ '/kendaraan/' + deleteId; //data dihapus berdasarkan id
 this.$http.delete(uri).then(response =>{
 this.snackbar = true;
 this.text = response.data.message;
 this.color = 'green'
 this.deleteDialog = false;
 this.getData();
 }).catch(error =>{
 this.errors = error
 this.snackbar = true;
 this.text = 'Try Again';
 this.color = 'red';
 })
 },
 setForm(){
 if (this.typeInput === 'new') {
 this.sendData()
 } else {
 console.log("dddd")
 this.updateData()
 }
 },
 resetForm(){
 this.form = {
 merk : '',
 type : '',
 licensePlate : ''
 }
 }
 },
 mounted(){
 this.getData();
 },
}
</script>