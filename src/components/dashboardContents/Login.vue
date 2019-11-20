<template>
  <v-app id="inspire">
    <v-content>
                <v-toolbar-title>Form Masuk</v-toolbar-title>
                <v-spacer />
              </v-toolbar>
                <v-form>
                  <v-text-field
                    v-model="form.email"
                    id="email"
                    label="Surel"
                    name="email"
                    type="text"
                  />
                  <v-spacer />
                  <v-spacer />
                  <v-spacer />
                  <v-text-field
                    v-model="form.password"
                    id="password"
                    label="Kata Sandi"
                    name="password"
                    type="password"
                  />
                </v-form>
                <v-spacer />
                <v-btn color="black" @click="masuk">Masuk</v-btn>
                <v-btn color="black" @click="daftar">Daftar</v-btn>
    </v-content>
  </v-app>
</template>

<script>
  export default {
    data(){
        return {
            form: { 
                email : '',
                password : ''
            },
            user : new FormData
        }
    },
    methods: {
        masuk(){
            if(this.form.email !="" || this.form.password != ""){
                this.user.append('email', this.form.email);
                this.user.append('password', this.form.password);
                var user = this.user
               this.$store
              .dispatch("login", user)
              .then(() => this.$router.push("/kendaraanController"))
              .catch(err => console.log(err))
            }else{
                alert("Isi semuanya!")
            }
        },
        daftar(){
            this.$router.push('/user')
        }
    }
  }
</script>