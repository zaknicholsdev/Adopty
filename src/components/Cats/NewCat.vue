<template>
  <div id="new-cat" class="container">
    <form @submit.prevent="saveCat">
      <div class="form-group">
        <label>Name</label>
        <input type="text" class="form-control" v-model="name" placeholder="Enter Name" />
      </div>
      <div class="form-group">
        <label>Age</label>
        <input type="text" class="form-control" v-model="age" placeholder="Enter Age" />
      </div>
      <div class="form-group">
        <label>Breed</label>
        <input type="text" class="form-control" v-model="breed" placeholder="Enter Breed" />
      </div>
      <div class="form-group">
        <label>Description</label>
        <input
          type="text"
          class="form-control"
          v-model="description"
          placeholder="Enter Description"
        />
      </div>
      <div class="form-group">
        <label for="inputState">Declawed</label>
        <select v-model="isDeclawed" class="form-control">
          <option disabled>Choose...</option>
          <option>Yes</option>
          <option>No</option>
        </select>
      </div>
      <div class="form-group">
        <label for="inputState">Spayed/Neutered</label>
        <select v-model="isNeuteredOrSpayed" class="form-control">
          <option disabled>Choose...</option>
          <option>Yes</option>
          <option>No</option>
        </select>
      </div>
      <div class="form-group">
        <label>Cat ID</label>
        <input disabled type="text" class="form-control" v-model="catId" placeholder="Enter ID" />
      </div>
      <div class="form-group">
        <label>Adoption Fee</label>
        <input
          type="text"
          class="form-control"
          v-model="adoptionFee"
          placeholder="Enter Adoption Fee"
        />
      </div>
      <div class="form-group">
        <label>Image URL</label>
        <input
          disabled
          type="text"
          class="form-control"
          v-model="imageUrl"
          placeholder="Enter Image URL"
        />
      </div>
      <button type="submit" class="btn primary-btn-color text-white mr-2">Submit</button>
      <router-link to="/cats" class="btn btn-secondary">Cancel</router-link>
    </form>
    <div>
      <p>Upload an image:</p>
      <input type="file" @change="previewImage" accept="image/*" />
    </div>
    <div>
      <p>Progress: {{uploadValue.toFixed()+"%"}}</p>
      <progress id="progress" :value="uploadValue" max="100"></progress>
    </div>
    <br />
    <div v-if="imageData!=null">
      <button class="btn primary-btn-color text-white mb-2" @click="onUpload">Upload</button>
      <br />
      <img class="img-fluid" :src="picture" />
    </div>
  </div>
</template>

<script>
import firebase from "../firebaseInit";
import { uuid } from "vue-uuid";

export default {
  name: "new-cat",
  data() {
    return {
      catId: uuid.v4(),
      date: new Date(),
      name: null,
      age: null,
      description: null,
      imageUrl: null,
      breed: null,
      isNeuteredOrSpayed: "No",
      isDeclawed: "No",
      adoptionFee: null,
      imageData: null,
      picture: null,
      uploadValue: 0
    };
  },
  methods: {
    previewImage(event) {
      this.uploadValue = 0;
      this.picture = null;
      this.imageData = event.target.files[0];
    },
    onUpload() {
      this.picture = null;
      this.imageUrl = null;
      const storageRef = firebase
        .storage()
        .ref(`${this.imageData.name}${Math.random()}`)
        .put(this.imageData);
      storageRef.on(
        `state_changed`,
        snapshot => {
          this.uploadValue =
            (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
        },
        error => {
          console.log(error.message);
        },
        () => {
          this.uploadValue = 100;
          storageRef.snapshot.ref.getDownloadURL().then(url => {
            this.picture = url;
            this.imageUrl = url;
          });
        }
      );
    },
    saveCat() {
      firebase
        .firestore()
        .collection("cats")
        .add({
          catId: this.catId,
          name: this.name,
          breed: this.breed,
          age: this.age,
          imageUrl: this.imageUrl,
          description: this.description,
          isNeuteredOrSpayed: this.isNeuteredOrSpayed,
          isDeclawed: this.isDeclawed,
          adoptionFee: this.adoptionFee
        })
        .then(() => {
          this.$router.push("/cats");
        })
        .catch(error => console.log(error));
    }
  }
};
</script>

<style scoped>
img {
  max-width: 400px;
  display: block;
  margin: auto;
}
</style>