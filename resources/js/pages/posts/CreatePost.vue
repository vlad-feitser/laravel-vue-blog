<template>
    <main class="create-post">
        <div class="container">
            <h1>Create Post!</h1>

            <div class="success-msg" v-if="success">
                <i class="fa fa-check"></i>
                Post created successfully
            </div>
            
            <div class="contact-form">
                <form @submit.prevent="submit">
                    <label for="title">Title</label>
                    <input type="text" id="title" v-model="fields.title">
                    <span v-if="errors.title" class="error">{{ errors.title[0] }}</span>
                    <br>

                    <label for="image"><span>Image</span></label>
                    <input type="file" id="image" @input="grabFile">
                    <span v-if="errors.file" class="error">{{ errors.file[0] }}</span>

                    <div class="preview">
                        <img :src="url" alt="">
                    </div>
                    <br>

                    <label for="categories"><span>Choose a category:</span></label>
                    <select v-model="fields.category_id" id="categories">
                        <option disabled value="">Select option</option>
                        <option v-for="category in categories" :key="category.id" :value="category.id">{{ category.name }}</option>
                    </select>
                    <span v-if="errors.category_id" class="error">{{ errors.category_id[0] }}</span>
                    <br>

                    <label for="body">Body</label>
                    <textarea v-model="fields.body" id="body"></textarea>
                    <span v-if="errors.body" class="error">{{ errors.body[0] }}</span>

                    <input type="submit" value="Submit" class="add-post-btn">
                </form>
            </div>
        </div>
    </main>
</template>

<script>
export default {
    data() {
        return {
            success: false,
            fields: {
                category_id: ''
            },
            errors: {},
            url: "",
            categories: []
        }
    },
    methods: {
        grabFile(e) {
            const file = e.target.files[0]
            this.fields.file = file
            this.url = URL.createObjectURL(file)
            URL.revokeObjectURL(file)
        },
        submit() {
            axios.post('/api/posts', this.fields, {
                headers: { "content-type": "multipart/form-data" }
            })
            .then(() => {
                this.fields = {}
                this.url = null
                this.fields.category_id = ""
                this.success = true
                this.errors = {}

                setTimeout(() => {
                    this.success = false
                }, 2500)
            }).catch((error) => {
                this.errors = error.response.data.errors
                this.success = false
            })
        }
    },
    mounted() {
    axios.get('/api/categories')
        .then((response) => {
            this.categories = response.data
        }).catch((error) => {
            console.log(error)
        })
    }
}
</script>

<style scoped>
.create-post {
    background-color: #fff;
    padding: 0 3vw;
}

.container input,
textarea,
select {
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
    box-sizing: border-box;
    margin-top: 6px;
    margin-bottom: 20px;
    font-size: 16px;
}

h1 {
    text-align: center;
    padding: 60px 0 50px 0;
}

.add-post-btn {
    background-color: black;
    color: white;
    border: none;
    cursor: pointer;
    transition: 0.3s ease;
}

.add-post-btn:hover {
    transform: translateY(-4px);
}

.preview img {
    max-width: 100%;
    max-height: 120px;
}
</style>