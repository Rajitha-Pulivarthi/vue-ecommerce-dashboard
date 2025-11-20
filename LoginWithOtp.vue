<script setup lang="ts">
import { RouterLink, useRouter } from 'vue-router';
import LoginLeftBanner from './LoginLeftBanner.vue';
import { z } from 'zod';
import { toTypedSchema } from '@vee-validate/zod';
import { useField, useForm } from 'vee-validate';
import { onMounted, ref } from 'vue';
import { endpoints } from '../../utility/RestApis';
import { httpClient } from '../../utility/HttpClient';
import useApiStateStore from "../../store/apistate";
import UserModel from '../Models/UserModel';

const router = useRouter();
const apiStateStore = useApiStateStore(); // ✅ Use imported store

const zodSchema = z.object({
    userName: z.string().min(1, { message: "Email /PhoneNumber is required" }),
    password: z.string().min(1, { message: "Password is required" }),
});

const validationSchema = toTypedSchema(zodSchema);

const { handleSubmit, resetForm, errors } = useForm({
    validationSchema,
    initialValues: {
        userName: '',
        password: '',
    }
});

onMounted(() => {
    resetForm();
})
const login = async (values: z.infer<typeof zodSchema>): Promise<loginResponse> => {
    const getParameters = {
        url: endpoints.auth,
        requiresToken: false,
        payload: {
            ...values,
            email: values.userName
        }
    }
    try {
        const response = await httpClient.post(getParameters);

        return response as loginResponse;
    } catch (error) {
        console.error('Error saving brand:', error);
        throw error;
    }
    console.log('Saving values:', values);
    // Simulate an API call

};
interface loginResponse {
    isLoginSuccess: boolean,
    message: string,
    userDetails: UserModel
}
const feedback = ref<string>("")

const onSubmit = handleSubmit(async (values) => {
    console.log('Form submitted with values:', values);
    try {
        login(values).then((p: loginResponse) => {
            if (p.isLoginSuccess) {
                let user: UserModel;
                user = p.userDetails;

                apiStateStore.setUser(user);
                router.push('/')
            }
            feedback.value = p.message;
            console.log(p.message);
        });
    } catch (error) {
        console.error('Submit error:', error);
    }
});

const { value: userName } = useField<boolean>('userName');
const { value: password } = useField<string | null>('password');


</script>
<template>
    <form @submit.prevent="onSubmit">
        <div class="container-fluid h-100">
            <div class="row h-100">
                <div class="col-md-6">
                    <div class="h-100 d-flex align-items-center justify-content-top p-5 flex-column">
                        <LoginLeftBanner />
                    </div>
                </div>
                <div class="col-md-6">
                    <div class="h-100 d-flex align-items-center justify-content-top p-5 flex-column">

                        <div style="width:60%;">
                            <h5 class="text-center">Welcome back!</h5>
                            <h3 class="text-center mb-4">Login to your account</h3>
                            <div class="mb-12">
                                <input type="text" placeholder="Enter Mobile Number / Email Id" class="input-field "
                                    v-model="userName" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.userName }}</span>
                                </div>
                            </div>
                            <div class="mb-12">
                                <input type="password" placeholder="Enter Password" class="input-field"
                                    v-model="password" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.password }}</span>
                                </div>
                            </div>
                            <div class="forgot-password">
                                <a href="#">Forgot Password</a>
                            </div>

                            <button class="login-btn" type="submit">Login</button>
                            <div class="error-container">
                                <span class="text-danger small">{{ feedback }}</span>
                            </div>
                            <p class="or-text">Or</p>

                            <button class="login-btn" type="submit" @click="$router.push('/Login')">
                                Login with Mobile Number
                            </button>
                            <p class="register-text">
                                Not registered? <RouterLink to="/signup">SignUp</RouterLink>
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </form>
</template>