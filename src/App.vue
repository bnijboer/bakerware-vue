<template>
    <div>
        <form @submit.prevent="storeUser">
            <label for="firstName" />
            <input id="firstName" type="text" v-model="firstName" name="firstName" placeholder="Enter first name" required>
            
            <label for="lastName" />
            <input id="lastName" type="text" v-model="lastName" name="lastName" placeholder="Enter last name" required>
            
            <button type="submit">Create user</button>
        </form>
        
        <form @submit.prevent="findUser">
            <label for="id" />
            <input id="id" type="text" v-model="id" name="id" placeholder="Enter user ID" required>
            
            <button type="submit">Find user</button>
        </form>
        
        <div v-if="watcherMessage.length">
            <h4 class="heading">{{ watcherMessage }}</h4>
            <div v-if="user !== undefined">
                <div class="row">
                    <div class="heading">Normal property:</div>
                    <div>First name: {{ user.firstName }}</div>
                </div>
                
                <div class="row">
                    <div class="heading">Computed property:</div>
                    <div>Full name: {{ fullName }}</div>
                </div>
            </div>
            <div v-else>No user found...</div>
        </div>
    </div>
</template>

<script lang="ts">
    import { defineComponent } from 'vue'
    
    interface UserInterface {
        firstName: String,
        lastName: String,
    }

    export default defineComponent({
        name: 'App',
        
        data() {
            return {
                firstName: '' as string,
                lastName: '' as string,
                id: '' as string,
                watcherMessage: '' as string,
                user: {
                    firstName: '',
                    lastName: '',
                } as UserInterface
            }
        },
        
        watch: {
            user() {
                const message: string = 'Results for user with ID: ' + this.id;
                
                this.watcherMessage = message;
            }
        },
        
        computed: {
            fullName(): string {
                return this.user.firstName + ' ' + this.user.lastName;
            }
        },
        
        methods: {
            storeUser() {
                const url: string = 'http://localhost:8000/users';
                
                const payload: object = {
                    firstName: this.firstName,
                    lastName: this.lastName
                };

                const data = new FormData();
                
                data.append('user', JSON.stringify(payload));
                
                fetch(url, {
                    method: 'POST',
                    body: data
                })
                .then(data => {
                    return data.json();
                })
                .then(response => {
                    console.log(response.message);
                });
            },
            
            findUser() {
                const url: string = 'http://localhost:8000/users/' + this.id;
                
                fetch(url)
                    .then(data => {
                        return data.json();
                    })
                    .then(response => {
                        this.user = response.user;
                        this.watcherMessage = response.message;
                    });
            }
        }
    })
</script>

<style scoped>
    .heading {
        font-weight: bold;
    }
    
    .row {
        margin-bottom: 10px;
    }
    
    form {
        margin-bottom: 20px;
    }
</style>