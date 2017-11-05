<template>
  <div>
    <div v-for="post in posts">
      <div class="card">
        <div class="card-content">
          <div class="media pull-right" v-if="user!=null">
            <div class="media-left">
              <figure class="image is-48x48">
                <img class="img-circle" :src="post.user.photoUrl" alt="Image">
              </figure>
            </div>
            <div class="media-content">
              <p class="title is-4">{{post.user.name}}</p>
              <p class="subtitle is-6">{{post.user.email}}</p>
            </div>
          </div>
          <h3>{{post.title}}</h3>
          <div class="content">
            {{post.message}}
            <br>
            <small>date</small>
          </div>

        </div>
        <footer class="card-footer" v-if="user!=null && post.uid===user.uid">
          <a class="card-footer-item" @click="editPost">Editar</a>
          <a class="card-footer-item" @click="deletePost">Remover</a>
        </footer>
      </div>
      <br/>
    </div>


  </div>
</template>

<script>
  import firebase from 'firebase'

  export default {
    name: 'home',
    data() {
      return {
        post: {
          title: ''
        },
        posts: []
      }
    },
    computed: {
      user() {
        return this.$store.getters.user
      }
    },
    mounted() {
      let t = this
      firebase.database().ref('posts').on('child_added', function (data) {
        let post = data.val()
        firebase.database().ref('/users/' + post.uid).once('value').then(function (snapshot) {
          post.user = snapshot.val()
          t.posts.push(post)
          console.log(post)
        })
      })
    },
    methods: {
      addPost: function () {
        this.post.uid = firebase.auth().currentUser.uid
        this.$firebaseRefs.posts.push(this.post).then(
          (r) => {
            this.post = { title: '', uid: '' }
          },
          (e) => {
            console.warn(e)
          }
        )
      },
      deletePost() {
        console.log('delete')
      },
      editPost() {
        console.log('edit')
      }
    }
  }

</script>


<style>

</style>
