<template>
<div class="newPost">
    <label for="content">O que você está pensando?</label>

    <textarea type="text" id="content" v-model="content" cols="30" rows="8" />

    <button @click="addNewPost">Enviar</button>
</div>
</template>

<script>
export default {
    name: "NewPost",
    data: function () {
        return {
            content: "",
            media: "",
            type: "",
        };
    },
    computed: {
        user() {
            return this.$store.state.user;
        },
    },
    methods: {
        async addNewPost() {
            if (this.content) {
                this.checkContent();

                await this.$store.commit("addPost", {
                    media: this.media,
                    type: this.type,
                    content: this.content,
                });
                this.resetNewPost();
            } else {
                alert("Preencha os campos necessários!");
            }
        },
        resetNewPost() {
            this.content = "";
            this.media = "";
            this.type = "";
        },
        checkContent() {
            if (this.haveYoutubeLink(this.content)) {
                this.focus = false;
                const videoID = this.content.substring(
                    this.content.lastIndexOf("?v") + 3,
                    this.content.lastIndexOf("")
                );
                this.media = videoID;
                this.type = "video";
                this.content = this.content.replace(
                    /((?:https?:)?\/\/)?((?:www|m)\.)?((?:youtube\.com|youtu.be))(\/(?:[\w-]+\?v=|embed\/|v\/)?)([\w-]+)(\S+)?$/,
                    ""
                );
            } else if (this.haveImageLink(this.content)) {
                const link = this.content.substring(
                    this.content.lastIndexOf("http"),
                    this.content.lastIndexOf("")
                );
                this.media = link;
                this.type = "image";

                this.content = this.content.replace(link, "");
            } else {
                this.type = "text";
                this.media = "";
            }
        },
        haveYoutubeLink(content) {
            return /((?:https?:)?\/\/)?((?:www|m)\.)?((?:youtube\.com|youtu.be))(\/(?:[\w-]+\?v=|embed\/|v\/)?)([\w-]+)(\S+)?$/.test(
                content
            );
        },
        haveImageLink(content) {
            return /(jpg|gif|png|JPG|GIF|PNG|JPEG|jpeg)$/.test(content);
        },
    },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

<style scoped>
.newPost {
    margin: 3rem 0;
    display: flex;
    flex-direction: column;
    padding: 1rem;
    background: #fff;
    text-align: left;
}

.post-image,
.post-video {
    width: 100%;
    height: 250px;
    object-fit: cover;
}

.newPost #content {
    margin: 1rem 0;
    resize: none;
}

.newPost #content+label {
    margin-left: 1rem;
}

.newPost #content {
    height: 45px;
    border-radius: 8px;
    border: none;
    background: #e8e8e8;
}

.input-group {
    margin-bottom: 1.2rem;
    width: 100%;
    overflow: hidden;
}

.newPost button {
    width: 100px;
    background: #00afb8;
    border: none;
    padding: 0.5rem;
    border-radius: 8px;
    color: #fff;
}

@media screen and (min-width: 767px) {
    .newPost {
        margin-top: 0px;
        margin-bottom: 1.3rem;
        border-radius: 0.3rem;
        border: 1px solid #c7c7c7;
    }

    .newPost label {
        color: #00afb8;
    }

    .newPost #content {
        height: 70px;
        outline: 0;
        padding: 15px;
        border: 1px solid #c7c7c7;
    }
}
</style>
