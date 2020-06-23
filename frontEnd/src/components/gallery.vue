<template>
  <div class="container">
    <div class="header">
      <div class="logo">
        <a>Gallery</a>
      </div>
      <div class="buttons">
        <button @click="logout()">
          Выход
        </button>
      </div>
    </div>
    <div class="input__wrapper">
      <input name="file" type="file" ref="file" id="input__file" class="input input__file"
             @change="fileInputChange()" accept="image/x-png,image/gif,image/jpeg">
      <label for="input__file" class="input__file-button">
        <span class="input__file-button-text">Выберите файл</span>
      </label>
    </div>
    <div class="gallery">
      <div class="gallery-image" v-for="item in photos" :key="item">
        <img :src="'http://localhost:3000/galery/api/image/'+item" alt="" @click="deleteImage(item)">
      </div>
    </div>
  </div>
</template>

<script>
    import axios from "axios";

    export default {
        name: "gallery",
        data() {
            return {
                photos: []
            }
        },
        methods: {
            logout() {
                this.$cookie.delete('name')
                this.$cookie.delete('password')
                this.$router.push('/auth')
            },
            async fileInputChange() {
                let file = this.$refs.file.files[0];
                let formData = new FormData();
                formData.append('file', file);
                // await axios.post( 'http://localhost:3000/galery/api/upload', formData).then(function (response) {
                //     console.log('123',response)
                // })
                const url = "http://localhost:3000/galery/api/upload";
                let requestParams = {
                    url: url,
                    method: "POST",
                    data: formData
                };
                await axios(requestParams).then(
                    resp => {
                        console.log()
                        this.photos.push(resp.data.file.filename)
                    },
                    err => {
                        console.log(err);
                    }
                );
            },
            async getGallery() {
                this.photos = []
                const url = "http://localhost:3000/galery/api/files";
                let requestParams = {
                    url: url,
                    method: "GET",
                };
                await axios(requestParams).then(
                    resp => {
                        for (let i = 0; i < resp.data.length; i++) {
                            this.photos.push(resp.data[i].filename)
                        }
                    },
                    err => {
                        console.log(err);
                    }
                );
            },
            deleteImage(id) {
                console.log(id)
            }
        },
        mounted() {
            this.getGallery()
        }
    }
</script>

<style lang="scss">
  @import "../assets/scss.scss";
  .input__wrapper {
    width: 100%;
    margin: 15px 0;
    text-align: center;
  }
  .input__file {
    opacity: 0;
    visibility: hidden;
    position: absolute;
  }
  .input__file-button {
    width: 100%;
    max-width: 220px;
    height: 50px;
    background-color: #0063dd;
    color: #fff;
    font-size: 20px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 5px;
    cursor: pointer;
    margin: 0 auto;
    transition: 0.2s;
    border: 2px solid #fff;
    &:hover {
      background-color: #fff;
      color: #0063dd;
      border: 2px solid #0063dd;
    }
  }
  .gallery {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    &-image {
      padding: 20px;
      img {
        width: 200px;
      }
    }
  }
</style>