<template>
  <v-container>
    <v-row wrap justify="center" align="center">
      <v-col :sm="12" :md="8">
        <p class="text-center">Content that can only be viewed by logged-in as {{user.email}} with uuid: {{user.uid}} </p>
        <div class="pa-5">
          {{firedata}}
          <v-btn
            block
            color="indigo darken-1"
            @click="getData"
            class="white--text"
          >
            Fetch Data
          </v-btn>
        </div>
        <div class="pa-5">
          <v-btn
            block
            outlined
            color="grey darken-3"
            @click="signOut"
          >
            Logout
          </v-btn>
        </div>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-file-input
          accept="image/*"
          placeholder="Pick an avatar"
          prepend-icon="mdi-camera"
          label="Avatar"
          :show-size="1000"
          v-model="imageData"
        ></v-file-input>
      </v-col>
      <v-col><v-btn @click="onUpload">upload</v-btn></v-col>
    </v-row>
    <v-row v-if="uploadinit">
      <v-col cols="12">
      
        <v-progress-circular
          :rotate="360"
          :size="100"
          :width="15"
          :value="uploadProgress"
          color="teal"
        >
          {{ uploadProgress }}
        </v-progress-circular>
      </v-col>
    </v-row>

  </v-container>
</template>

<script>
import {db, storage} from '~/plugins/firebase'
import { mapGetters } from 'vuex'

export default {
  middleware: "authenticated",
  async asyncData() {
    try {
      const test = await db.collection("message").doc("message").get().then(
          res => {
             return res.data()
          }
        )
        return {test}

    } catch (error) {
      error(error)
    }
  },
 data() {
   return {
     firedata: null,
     rules: [
        value => !value || value.size < 2000000 || 'Avatar size should be less than 2 MB!',
      ],
      imageData: null,
      uploadinit: false,
      uploadProgress: 0,
      img1: null,
   }
 },
 computed: {
   ...mapGetters([ 'user' ])
 },
  methods: {
    signOut: function(err) {
      this.$store
        .dispatch('signOut')
        .then(() => {
          this.$router.push({
            name: 'login'
          })
        })
        .catch((err) => {
          alert(err.message)
        })
    },
    getData() {
      const userEmail = this.$store.getters.user.email
      db.collection("Users").where("email", "==" , userEmail).get().then(
        res => {
          res.forEach(doc => {
            console.log(doc.data());
            const subdoc = {

            }
          });
        }
      )
    },
    onUpload(){
        this.img1=null
        this.uploadProgress = 0
        const storageRef=storage.ref(`${this.user.uid}_${this.imageData.name}`).put(this.imageData);
        storageRef.on(`state_changed`,snapshot=>{
          this.uploadinit = true
        this.uploadProgress = (snapshot.bytesTransferred/snapshot.totalBytes)*100;
          }, error=>{console.log(error.message)},
        ()=>{this.uploadValue=100;
            storageRef.snapshot.ref.getDownloadURL().then((url)=>{
                this.img1 =url;
                console.log(this.img1)
                this.uploadinit = false
              });
            }      
          );
    },
  }
}
</script>
