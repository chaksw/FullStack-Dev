<template>
    <div class="container">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Sign up</h1>
                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label for="email">Email</label>
                        <div class="control">
                            <input
                                type="email"
                                class="input"
                                name="email"
                                id="email"
                                v-model="username" />
                        </div>
                    </div>
                    <div class="field">
                        <label for="password1">Password</label>
                        <div class="control">
                            <input
                                type="password"
                                class="input"
                                name="password1"
                                id="password1"
                                v-model="password1" />
                        </div>
                    </div>
                    <div class="field">
                        <label for="password2">Repeat password</label>
                        <div class="control">
                            <input
                                type="password"
                                class="input"
                                name="password2"
                                id="password2"
                                v-model="password2" />
                        </div>
                    </div>
                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="error in errors" v-bind:key="errors">
                            {{ error }}
                        </p>
                    </div>
                    <div class="field">
                        <div class="control">
                            <button class="button is-success">Submit</button>
                        </div>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import { toast } from "bulma-toast";
export default {
    name: "SignUp",
    data() {
        // with return an object with initial data defined below
        return {
            username: "",
            password1: "",
            password2: "",
            errors: [],
        };
    },
    methods: {
        async submitForm() {
            this.errors = [];
            if (this.username === "") {
                this.errors.push("The username is missing");
            }
            if (this.password1 === "") {
                this.errors.push("The password is too short");
            }
            if (this.password1 !== this.password2) {
                this.errors.push("The passwords are not matching");
            }
            if (!this.errors.length) {
                this.$store.commit("setIsLoading", true);
                const formData = {
                    username: this.username,
                    password: this.password1,
                };

                await axios
                    .post("/api/v1/users/", formData)
                    .then((response) => {
                        toast({
                            message: "Account was created, please log in",
                            type: "is-success",
                            dismissible: true,
                            pauseOnHover: true,
                            duration: 2000,
                            position: "bottom-right",
                        });
                        // this.$router.push("/log-in");
                        this.$router.push({ name: "LogIn" });
                    })
                    .catch((error) => {
                        if (error.response) {
                            for (const property in error.response.data) {
                                this.errors.push(
                                    `${property}:${error.response.data[property]}`
                                );
                            }
                        } else if (error.message) {
                            this.errors.push(
                                "Something went wrong. Please try again!"
                            );
                        }
                    });
                this.$store.commit("setIsLoading", false);
            }
        },
    },
};
</script>
