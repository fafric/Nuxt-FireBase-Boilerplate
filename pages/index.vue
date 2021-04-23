<template>
  <v-container>
    <v-row wrap justify="center" align="center">
      <v-col :sm="12" :md="8">
        <p class="text-center">Content that can only be viewed by logged-in users</p>
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
  </v-container>
</template>

<script>
import {db} from '~/plugins/firebase'
export default {
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
     firedata: null
   }
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
    }
  }
}
</script>
