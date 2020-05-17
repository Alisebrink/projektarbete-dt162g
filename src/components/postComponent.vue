
  <template>
  <div class="container">
    <h1>Min att-göra-lista</h1>
    <div class="create-post">
      <input type="text" id="create-post" v-model="text">
      <button class="add-task-button" v-on:click="createPost">Lägg till en uppgift</button>
    </div>
    <!-- Skapa post här -->
    <hr>
    <p class="error" v-if="error">{{error}}</p>
    <div class="posts-container">
      <div class="post"
        v-for="(post, index) in posts"
        v-bind:item="post"
        v-bind:index="index"
        v-bind:key="post._id"
      >
      <input type="checkbox" class="finish-task"
      v-model="post.isFinished"
      >
      <p class="text"
        v-if ="!post.editmode"
        v-on:click="enterEditmode(post)"
        v-bind:class="{finishedTask: post.isFinished}"
        >
        {{post.text}}
      </p>
      <input class="input-field"
        v-else
        v-model="post.text"
        >
        <p class="text-date">Tillagd {{`${post.createdAt.getDate()}/${post.createdAt.getMonth()}/${post.createdAt.getFullYear()}`}}</p>
      <button class="update-button"
        v-on:click="updatePost(post._id, post.text)">Uppdatera</button>
      
      <button class="delete-button"
        v-on:click="deletePost(post._id)">Radera</button>

      
    </div>
  </div>
  </div>
</template>

<script>
import PostService from '../postService';

export default {
  name: 'postComponent',
  data() {
    return {
      posts: [],
      error: '',
      text: '',
      isFinished:true,
    } 
  },
  async created() {
    try {
      this.posts = await PostService.getPosts();
    } catch(err) {
      this.error = err.message;
    }
  },
  methods: {
    async createPost() {
      await PostService.insertPost(this.text);
      this.posts = await PostService.getPosts();
    },
    async deletePost(id) {
      await PostService.deletePost(id);
      this.posts = await PostService.getPosts();
    },
    async updatePost(id, text) {
      await PostService.updatePost(id, text);
      this.posts = await PostService.getPosts();
    },
    enterEditmode(post) {
      post.editmode = true;
      this.posts = [...this.posts];
    },
  },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

div.container { 
  max-width: 800px; 
  margin: 0 auto;
  padding:15px;
}

p.error { 
  border: 1px solid #ff5b5f; 
  background-color: #ffc5c1; 
  padding: 10px; 
  margin-bottom: 15px; 
}

.delete-button, .update-button, .finish-task, .add-task-button {
  padding:5px 15px;
  margin:5px;
  font-size: 10px;
  font-weight:700;
  letter-spacing: 1px;
  text-transform:uppercase;
  color:white;
  background:#027A9F;
  border:none;
  border-radius: 5px;
  height:40px;
  width:auto;
}

.delete-button:hover, .update-button:hover, .finish-task:hover, .add-task-button:hover {
  color:black;
  background:#FFAA01;
  cursor:pointer;
  transition:0.2s;
}

div.post { 
  position: relative; 
  background-color: white;

  padding:10px;
  margin: 20px 15px;
  display:flex;
  border-radius: 5px;
}

div.posts-container {
  padding:5px;
  background:#F8F8F8;
}

div.created-at {  
  background-color: darkgreen; 
}

p.text {
  font-size: 16px; 
  font-weight: 700;
  width:60%;
  text-align: left;
  margin-left:10px;
}

.input-field {
  width:70%;
  height:20px;
  margin-top:10px;
  margin-bottom:10px;
  margin-right:15px;
  padding:5px 15px;
  border-radius:5px;
  border:1px solid #FFAA01;
}

p.text-date {
  font-size:12px;
  text-align: center;
  color:#adadad;
  margin-right:15px;
}

.finishedTask {
  text-decoration: line-through;
  color:#FFAA01;
}

div.create-post {
  padding:10px;
}

div.create-post input[type=text] {
  height:25px;
  width:300px;
  margin-left:15px;
  margin-right:15px;
  border:1px solid #FFAA01;
  border-radius: 5px;
  padding: 5px 10px;
}
</style>
