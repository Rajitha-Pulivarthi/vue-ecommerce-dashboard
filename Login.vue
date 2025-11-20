<script setup lang="ts">
import { RouterLink, useRouter } from "vue-router";
import LoginLeftBanner from "./LoginLeftBanner.vue";
// import { useI18n } from 'vue-i18n'
import { useField, useForm } from "vee-validate";
import { z } from "zod";
import { toTypedSchema } from "@vee-validate/zod";
import { onMounted, ref } from "vue";
import { endpoints } from "../../utility/RestApis";
import { httpClient } from "../../utility/HttpClient";
import useApiStateStore from "../../store/apistate";
import UserModel from '../Models/UserModel';

// const { t } = useI18n()
const apiStateStore = useApiStateStore(); // ✅ Use imported store

const zodSchema = z.object({
  phoneNumber: z.string().min(1, { message: "Phonenumber is required" }),
  otp: z.string().optional()
});

const enableOtp = ref<boolean>(false)
const showUserId = ref(true);
const validationSchema = toTypedSchema(zodSchema);

const { handleSubmit, resetForm, errors } = useForm({
  validationSchema,
  initialValues: {
    phoneNumber: '',
    otp: '',
  }
});
onMounted(() => {
  resetForm();
})

const { value: phoneNumber } = useField<boolean>('phoneNumber');
const { value: otp } = useField<string | null>('otp');
const { value: userid } = useField<string | null>('userid');

//const router = useRouter();

interface loginResponse {
  isOtpSent: boolean,
  loginState: number,
  message: string,
  errors: string[],
  userDetails: UserModel
}
const initiateLogin = async (values: z.infer<typeof zodSchema>): Promise<loginResponse> => {
  const getParameters = {
    url: endpoints.auth,
    requiresToken: false,
    payload: {
      ...values,
    },
    path: '/initiateOtp'
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
const validateOtp = async (values: z.infer<typeof zodSchema>): Promise<loginResponse> => {
  const getParameters = {
    url: endpoints.auth,
    requiresToken: false,
    payload: {
      ...values,
    },
    path: '/validateOtp'
  }
  try {
    const response = await httpClient.post(getParameters);
    return response as loginResponse;
  } catch (error) {
    console.error('Error saving brand:', error);
    throw error;
  }  
  // Simulate an API call   
};
const feedback = ref<string>("")
const onSubmit = handleSubmit(async (values) => {

  console.log('Form submitted with values:', values);
  try {
    if (!enableOtp.value) {
      initiateLogin(values).then((p: loginResponse) => {
        if (p.isOtpSent) {
          enableOtp.value = p.isOtpSent
        }
        feedback.value = p.message;
        console.log(p.message);
      });
    } else {
      validateOtp(values).then((p: loginResponse) => {
        if (p.userDetails) {
          let user: UserModel;
          user = p.userDetails;

          apiStateStore.setUser(user);          
          apiStateStore.resetCartWithLogin(p.userDetails.id);
        
        //  router.push('/')
        window.location.href = '/'; // Redirect to home page
        } else {
          console.error('User details not found in response');
        }
        feedback.value = p.message;
        console.log(p.message);
      });
    }
  } catch (error) {
    console.error('Submit error:', error);
  }
});

</script>
<template>
  <form @submit.prevent="onSubmit">
    <div class="container-fluid">
      <div class="row h-100">
        <div class="col-md-6">
          <div class="h-100 d-flex align-items-center justify-content-top p-5 flex-column">
            <LoginLeftBanner />
          </div>
        </div>
        <div class="col-md-6">
          <div class="h-100 d-flex align-items-center justify-content-top p-5 flex-column">
            <div style="width: 60%">
              <h5 class="text-center">Welcome back!</h5>
              <h3 class="text-center mb-4">Login to your account</h3>
              <label class="labeltext m-2">Registered Mobile Number</label>
              <div class="mb-12">
                <input type="text" class="form-control loginform mb-3" placeholder="91" v-model="phoneNumber"
                  @input="showUserId = !phoneNumber" />
                <div class="error-container">
                  <span class="text-danger small">{{ errors.phoneNumber }}</span>
                </div>
              </div>
              <div class="mb-12" v-if="enableOtp">
                <input type="password" class="form-control loginform mb-3" placeholder="****" v-model="otp" />
                <div class="error-container">
                  <span class="text-danger small">{{ errors.phoneNumber }}</span>
                </div>
              </div>
              <div v-if="showUserId">
                <div class="d-flex align-items-center">
                  <hr class="flex-grow-1" />
                  <span class="mx-2">Or</span>
                  <hr class="flex-grow-1" />
                </div>


                <label class="labeltext m-2">User ID</label>
                <div class="input-group mb-3">
                  <RouterLink to="/LoginWithOtp" class="register-link">
                    <input type="text" v-model="userid" class="form-control loginformpass" placeholder="User ID" />
                  </RouterLink>
                  <button class="btn btn-outline-secondary borderradious">
                    →
                  </button>
                </div>
              </div>
              <div class="text-end mb-3">
                <a href="#" class="forgot-password">Forgot Password</a>
              </div>
              <button class="btn btn-success w-100 mb-3" type="submit">Login</button>
              <p class="text-center">
                Not registered?
                <RouterLink to="/signup" class="register-link">SignUp</RouterLink>
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </form>
</template>
