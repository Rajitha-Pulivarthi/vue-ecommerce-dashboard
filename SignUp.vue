<script setup lang="ts">
import LoginLeftBanner from './LoginLeftBanner.vue';
import { toTypedSchema } from '@vee-validate/zod';
import { useForm, useField } from 'vee-validate';
import { onMounted } from 'vue';
import { z } from 'zod';
import { endpoints } from '../../utility/RestApis';
import { httpClient } from '../../utility/HttpClient';
import UserModel from '../Models/UserModel';
import { useRouter } from 'vue-router';

const router = useRouter();

// Add debounce function
// const debounce = <T extends (...args: any[]) => Promise<any>>(fn: T, delay: number): T => {
//     let timeoutId: ReturnType<typeof setTimeout>;
//     return ((...args: any[]) => {
//         clearTimeout(timeoutId);
//         return new Promise((resolve) => {
//             timeoutId = setTimeout(async () => resolve(await fn(...args)), delay);
//         });
//     }) as T;
// };

// Debounced email validation
// const validateEmailDebounced = debounce(async (value: string) => {
//     if (!value || !/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value)) {
//         return 1; // Skip API call if email format is invalid
//     }
//     return await validateEmail(value);
// }, 500);

// const validateUserNameDebounced = debounce(async (value: string) => {
//     if (!value) {
//         return 1; // Skip API call if email format is invalid
//     }
//     return await validateUserName(value);
// }, 500);

// const { t } = useI18n();


// const validateEmail = async (value:string): Promise<number> => {
//     const getParameters = {
//         url: endpoints.user,
//         requiresToken: false,
//         path:'/GetByEmail/'+value
//     }
//     try {
//         console.log('Fetching brands from API...');
//         const response = await httpClient.get<number>(getParameters);      
//         return response;
//     } catch (error) {
//         console.error('Error fetching items:', error);
//         return 0;
//     }
// };
// const validateUserName = async (value:string): Promise<number> => {
//     const getParameters = {
//         url: endpoints.user,
//         requiresToken: false,
//         path:'/GetByUserName/'+value
//     }
//     try {
//         console.log('Fetching brands from API...');
//         const response = await httpClient.get<number>(getParameters);  

//         return response;
//     } catch (error) {
//         console.error('Error fetching items:', error);
//         return 0;
//     }
// };

const validationSchema = toTypedSchema(
    z.object({
        id: z.string().optional(),
        fullName: z.string().min(1, { message: "Name is required" }),
        email: z.string()
            .min(1, { message: "Email is required" })
            .email({ message: "Invalid email format" }),
        // .superRefine(async (email, ctx) => {
        //     try {
        //         const response = await validateEmailDebounced(email);
        //         if (response === 1) {
        //             ctx.addIssue({
        //                 code: z.ZodIssueCode.custom,
        //                 message: "Email already exists"
        //             });
        //             return false;
        //         }
        //         return true;
        //     } catch (error) {
        //         console.error('Email validation error:', error);
        //         return true;
        //     }
        // }),
        phoneNumber: z.string().min(1, { message: "PhoneNumber is required" }),
        userName: z.string().min(1, { message: "Username is required" }),
        // .superRefine(async (userName, ctx) => {
        //     try {
        //         const response = await validateUserNameDebounced(userName);
        //         if (response === 1) {
        //             ctx.addIssue({
        //                 code: z.ZodIssueCode.custom,
        //                 message: "Username already exists"
        //             });
        //             return false;
        //         }
        //         return true;
        //     } catch (error) {
        //         console.error('Email validation error:', error);
        //         return true;
        //     }
        // }),
        password: z.string().nullable(),
        address: z.string().nullable(),
        confPwd: z.string().nullable(),
    }).refine((data) => data.password === data.confPwd, { message: 'Password dont match', path: ['confPwd'] })
);


const { handleSubmit, resetForm, errors } = useForm({
    validationSchema,
    initialValues: {
        id: '',
        fullName: '',
        email: '',
        phoneNumber: '',
        userName: '',
        password: '',
        address: '',
        confPwd: ''
    }
});

const save = async (values: any) => {
    const getParameters = {
        url: endpoints.user,
        requiresToken: false,
        payload: {
            ...values
        }
    }
    try {
        const response = await httpClient.post<UserModel>(getParameters);
        return response;
    } catch (error) {
        console.error('Error saving brand:', error);
        throw error;
    }
    console.log('Saving values:', values);
};

const onSubmit = handleSubmit(async (values) => {

    console.log('Form submitted with values:', values);
    try {
        save(values).then((p: any) => {
            console.log(p);
            router.push('/loginwithotp')
        });
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
                                <input type="password" placeholder="Confirm Password" class="input-field"
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

.mb-12 {
    margin-top: 8px;
}
</style>