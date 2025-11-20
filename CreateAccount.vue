<script setup lang="ts">
import LoginLeftBanner from './LoginLeftBanner.vue';
import { toTypedSchema } from '@vee-validate/zod';
import { useForm, useField } from 'vee-validate';
import { onMounted } from 'vue';
import { z } from 'zod';

const validationSchema = toTypedSchema(
    z.object({
        id: z.number().optional(),
        fullName: z.string().min(1, { message: "Name is required" }),
        email: z.string().min(1, { message: "email is required" }),
        phoneNumber: z.string().min(1, { message: "PhoneNumber is required" }),
        userName: z.string().min(1, { message: "User Name is required" }),
        password: z.string().nullable(),
        role: z.number().optional(),
        address: z.string().nullable(),
        confPwd: z.string().nullable()
    })
);


const { handleSubmit, resetForm, errors } = useForm({
    validationSchema,
    initialValues: {
        id: 0,
        fullName: '',
        email: '',
        phoneNumber: '',
        userName: '',
        role: 0,
        password: null,
        address: null,
        confPwd: null
    }
});

const save = async (values: any) => {
    console.log('Saving values:', values);
    // Simulate an API call

};

const onSubmit = handleSubmit(async (values) => {

    console.log('Form submitted with values:', values);
    try {
        await save(values);
    } catch (error) {
        console.error('Submit error:', error);
    }
});

onMounted(() => {
    resetForm();
});

const { value: fullName } = useField<string>('fullName');
const { value: email } = useField<string>('email');
const { value: phoneNumber } = useField<string>('phoneNumber');
const { value: userName } = useField<string>('userName');
// const { value: role } = useField<string | null>('role');
const { value: password } = useField<string | null>('password');
const { value: address } = useField<string | null>('address');
const { value: confPwd } = useField<string | null>('confPwd');

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
                            <h3 class="text-center mb-4">Signup Your Account</h3>
                            <div class="mb-12">
                                <input type="text" placeholder="Name" class="input-field signupfield "
                                    v-model="fullName" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.fullName }}</span>
                                </div>
                            </div>
                            <div class="mb-12">
                                <input type="LoginName" placeholder="Login Name" class="input-field"
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
                            <div class="mb-12">
                                <input type="ConfirmPassword" placeholder="Confirm Password" class="input-field"
                                    v-model="confPwd" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.confPwd }}</span>
                                </div>
                            </div>
                            <!-- <div class="mb-12">
                                <input type="UserType" placeholder="User Type" class="input-field" v-model="role" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.role }}</span>
                                </div>
                            </div> -->
                            <div class="mb-12">
                                <input type="Emai" placeholder="Emai ld" class="input-field" v-model="email" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.email }}</span>
                                </div>
                            </div>
                            <div class="mb-12">
                                <input type="Phone" placeholder="Phone" class="input-field" v-model="phoneNumber" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.phoneNumber }}</span>
                                </div>
                            </div>
                            <div class="mb-12">
                                <input type="Address" placeholder="Address" class="input-field" v-model="address" />
                                <div class="error-container">
                                    <span class="text-danger small">{{ errors.address }}</span>
                                </div>
                            </div>

                            <button class="login-btn" type="submit">SignUp</button>

                            <p class="register-text">
                                If you already registered <RouterLink to="/loginwithotp">Go to login Page</RouterLink>
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </form>
</template>
<style scoped>
.error-container {
    min-height: 20px;

}

.text-danger.small {
    font-size: 12px;
    display: block;
}
</style>