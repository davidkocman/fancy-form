<script setup lang="ts">
import { onBeforeMount, reactive, ref, computed } from '@vue/runtime-core'
import Language from './Language.vue'
import Country from './Country.vue'
import CountryType from '../types/CountryType'
import FormState from '../types/FormState';
import useVuelidate from '@vuelidate/core'
import { required, email, minLength, sameAs, helpers } from '@vuelidate/validators'

const countries = ref<CountryType[] | null>(null)
const selectedLang = ref<string>('')
const selectedCountry = ref<string>('')
const langError = ref<boolean>(false)
const countryError = ref<boolean>(false)
const registered = ref<boolean>(false)
const formState = reactive<FormState>({
  firstName: '',
  secondName: '',
  email: '',
  password: '',
  confirmPassword: '',
  terms: ''
})

const rules = computed(() => {
  return {
    firstName: { required },
    secondName: { required },
    email: { required, email },
    password: { required, minLength: minLength(6) },
    confirmPassword: { required, sameAs: sameAs(formState.password) },
    terms: { required },
  }
})

const v$ = useVuelidate(rules, formState)

function onLangSelected(value: string) {
  selectedLang.value = value
  langError.value = false
}
function onCountrySelected(value: string) {
  selectedCountry.value = value
  countryError.value = false
}
async function fetchCountries() {
  try {
    let response = await fetch('https://restcountries.com/v2/all?fields=name')
    countries.value = await response.json()
  } catch (e: any) {
    console.log(e)
  }
}
function handleSubmit() {
  if (selectedLang.value === '') langError.value = true
  if (selectedCountry.value === '') countryError.value = true
  v$.value.$validate()
  if (v$.value.$invalid) return
  registered.value = true
}

onBeforeMount(() => {
  fetchCountries()
})
</script>

<template>
  <div class="success" :class="{ show: registered }">
    <div class="icon">
      <img src="../assets/check.svg" />
    </div>
    <h2>Success registration</h2>
    <h3>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris lobortis gravida ex, at maximus lorem condimentum ut. Cras purus mauris, convallis vitae sollicitudin sed, fringilla lobortis lorem.</h3>
  </div>
  <div class="form-wrapper" :class="{ hide: registered }">
    <h1>Registration Form</h1>
    <div class="form" novalidate>
      <label for="email" class="input">
        <input
          type="text"
          id="email"
          placeholder="&nbsp;"
          v-model="formState.email"
          :class="{ error: v$.email.$errors.length }"
        />
        <span class="label">Email</span>
        <div class="input-errors" v-for="error of v$.email.$errors" :key="error.$uid">
          <div class="error-msg">{{ error.$message }}</div>
        </div>
      </label>
      <div class="input-group">
        <label for="first-name" class="input">
          <input
            type="text"
            id="first-name"
            placeholder="&nbsp;"
            v-model="formState.firstName"
            :class="{ error: v$.firstName.$errors.length }"
          />
          <span class="label">First Name</span>
          <div class="input-errors" v-for="error of v$.firstName.$errors" :key="error.$uid">
            <div class="error-msg">{{ error.$message }}</div>
          </div>
        </label>
        <label for="last-name" class="input">
          <input
            type="text"
            id="last-name"
            placeholder="&nbsp;"
            v-model="formState.secondName"
            :class="{ error: v$.secondName.$errors.length }"
          />
          <span class="label">Second Name</span>
          <div class="input-errors" v-for="error of v$.secondName.$errors" :key="error.$uid">
            <div class="error-msg">{{ error.$message }}</div>
          </div>
        </label>
      </div>
      <div class="input-group">
        <Language @langSelected="onLangSelected" :error="langError" />
        <Country
          v-if="countries"
          :values="countries"
          :error="countryError"
          @countrySelected="onCountrySelected"
        />
      </div>
      <div class="input-group">
        <label for="password" class="input pass">
          <input
            type="password"
            id="password"
            placeholder="&nbsp;"
            v-model="formState.password"
            :class="{ error: v$.password.$errors.length }"
          />
          <span class="label">Password</span>
          <div class="input-errors" v-for="error of v$.password.$errors" :key="error.$uid">
            <div class="error-msg">{{ error.$message }}</div>
          </div>
        </label>
        <label for="password-confirm" class="input pass">
          <input
            type="password"
            id="password-confirm"
            placeholder="&nbsp;"
            v-model="formState.confirmPassword"
            :class="{ error: v$.confirmPassword.$errors.length }"
          />
          <span class="label">Confirm Password</span>
          <div class="input-errors" v-for="error of v$.confirmPassword.$errors" :key="error.$uid">
            <div class="error-msg">{{ error.$message }}</div>
          </div>
        </label>
      </div>
      <div class="private-profile">
        <label for="private-profile">Private Profile</label>
        <div class="button-cover">
          <div class="button b2" id="button-13">
            <input type="checkbox" id="private-profile" class="checkbox" />
            <div class="knobs">
              <div class="knob"></div>
            </div>
            <div class="layer"></div>
          </div>
        </div>
      </div>
      <div class="bottom">
        <button @click="handleSubmit">Sign up</button>
        <div class="terms">
          <input type="checkbox" id="terms" v-model="formState.terms" />
          <label for="terms">Creating an account means you’re okay with our Privacy Policy.</label>
          <div class="terms-errors" v-if="v$.terms.$errors.length">
            <div class="error-msg">You must accept terms</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.success {
  font-family: "DM Sans", sans-serif;
  width: 90%;
  margin: 0 auto;
  background: #ffffff;
  box-shadow: 0px 40px 60px -33px rgba(0, 0, 0, 0.04);
  border-radius: 25px;
  padding: 20px;
  position: absolute;
  top: -1500px;
  left: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  transition: all 0.5s ease-out;
  z-index: 9;
  text-align: center;
  transform: translateX(-50%);
  @media (min-width: 1024px) {
    width: 874px;
    padding: 94px 106px;
  }
  &.show {
    top: 30%;
    transform: translate(-50%, -50%);
    @media (min-width: 1024px) {
      top: 40%;
    }
  }
  .icon {
    background-color: #17ba87;
    width: 108px;
    height: 108px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
  }
  h2 {
    font-weight: bold;
    font-size: 30px;
    line-height: 36px;
    @media (min-width: 1024px) {
      font-size: 60px;
      line-height: 66px;
    }
  }
  h3 {
    font-weight: normal;
    font-size: 18px;
    line-height: 32px;
  }
}
.form-wrapper {
  width: 100%;
  margin: 0 auto;
  background: #ffffff;
  box-shadow: 0px 40px 60px -33px rgba(0, 0, 0, 0.04);
  border-radius: 25px;
  padding: 20px;
  transition: all 0.3s ease;
  @media (min-width: 1024px) {
    width: 874px;
    padding: 94px 106px;
    margin: 40px auto 0;
  }
  &.hide {
    opacity: 0;
    transform: scale(0);
  }
  h1 {
    text-align: center;
  }
  .form {
    .input {
      position: relative;
      margin: auto;
      width: 100%;
      border-radius: 3px;
      overflow: visible;
      &.pass {
        &::after {
          content: "";
          background-image: url(/src/assets/pass.svg);
          position: absolute;
          top: 21px;
          right: 15px;
          display: block;
          width: 24px;
          height: 21px;
          background-repeat: no-repeat;
        }
      }
      .label {
        position: absolute;
        top: 20px;
        left: 12px;
        font-size: 16px;
        color: #76879e;
        font-size: 16px;
        transform-origin: 0 0;
        transform: translate3d(7px, -26px, 0);
        transition: all 0.2s ease;
        pointer-events: none;
      }
      input {
        -webkit-appearance: none;
        appearance: none;
        border: 1px solid #f2f5f8;
        width: 100%;
        border-radius: 14px;
        font-family: inherit;
        padding: 12px 18px 0 18px;
        height: 64px;
        font-size: 16px;
        background: #f6f8fa;
        color: #1b2c45;
        transition: all 0.15s ease;
        &:hover {
          border: 1px solid #d5dbe2;
        }
        &:not(:placeholder-shown) {
          + .label {
            transform: translate3d(7px, -34px, 0) scale(0.75);
          }
        }
        &:focus {
          outline: none;
          border-bottom: 2px solid #004eef;
          + .label {
            transform: translate3d(7px, -34px, 0) scale(0.75);
          }
        }
        &.error {
          border: 1px solid #f43c3c;
        }
      }
      .input-errors {
        position: relative;
        left: 10px;
        .error-msg {
          color: #f43c3c;
        }
      }
    }
    .input-group {
      margin: 26px 0;
      display: grid;
      grid-template-columns: 1fr;
      gap: 26px;
      @media (min-width: 1024px) {
        grid-template-columns: 1fr 1fr;
      }
      .label {
        transform: translate3d(7px, 0, 0);
      }
      input {
        &:not(:placeholder-shown) {
          + .label {
            transform: translate3d(7px, -10px, 0) scale(0.75);
          }
        }
        &:focus {
          + .label {
            transform: translate3d(7px, -10px, 0) scale(0.75);
          }
        }
      }
    }
    .private-profile {
      margin: 26px 0;
      padding-bottom: 26px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      border-bottom: 1px solid #f1f3f5;
      label {
        font-weight: bold;
        font-size: 16px;
      }
      .button-cover {
        position: relative;
        top: 11px;
        &::before {
          counter-increment: button-counter;
          content: counter(button-counter);
          position: absolute;
          right: 0;
          bottom: 0;
          color: #d7e3e3;
          font-size: 12px;
          line-height: 1;
          padding: 5px;
        }
        .button-cover,
        .knobs,
        .layer {
          position: absolute;
          top: 0;
          right: 0;
          bottom: 0;
          left: 0;
        }
        .button {
          position: relative;
          top: 50%;
          width: 60px;
          height: 30px;
          margin: -20px auto 0 auto;
          overflow: hidden;
          &.b2 {
            border-radius: 2px;
          }
        }
        .checkbox {
          position: relative;
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
          opacity: 0;
          cursor: pointer;
          z-index: 3;
        }
        .knobs {
          z-index: 2;
        }

        .layer {
          width: 100%;
          background-color: #f2f5f8;
          transition: 0.3s ease all;
          z-index: 1;
          border-radius: 10px;
        }
      }
      #button-13 {
        .knobs {
          &::before,
          &::after,
          .knob {
            position: absolute;
            top: 4px;
            width: 20px;
            height: 10px;
            font-size: 11px;
            text-align: center;
            line-height: 1;
            padding: 9px 4px;
            border-radius: 2px;
            transition: 0.3s ease all;
          }
          &::before,
          &::after {
            color: #4e4e4e;
            z-index: 1;
            top: 1px;
          }
          &::before {
            content: "NO";
            left: 4px;
            opacity: 1;
          }
          &::after {
            content: "YES";
            right: 4px;
            opacity: 0;
          }
          .knob {
            width: 19px;
            left: 37px;
            height: 24px;
            top: 3px;
            background-color: #ffffff;
            box-shadow: 0px 2px 2px -1px rgba(0, 0, 0, 0.12);
            border-radius: 7px;
            z-index: 2;
            color: #76879e;
          }
        }
        .checkbox {
          &:checked {
            + .knobs {
              &::before {
                opacity: 0;
              }
              &::after {
                opacity: 1;
                color: #ffffff;
              }
              .knob {
                left: 4px;
              }
            }
            ~ .layer {
              background-color: #004eef;
            }
          }
        }
      }
    }
  }
  .bottom {
    margin: 26px 0;
    display: grid;
    grid-template-columns: 1fr;
    gap: 50px;
    @media (min-width: 1024px) {
      grid-template-columns: 268px 1fr;
      gap: 30px;
    }
    button {
      order: 2;
      width: 100%;
      height: 62px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #ffffff;
      font-size: 16px;
      font-weight: bold;
      box-shadow: 0px 4px 14px rgba(0, 78, 239, 0.19);
      border-radius: 16px;
      background: linear-gradient(269.72deg, #004eef 0.1%, #8600ef 96.94%);
      transition: all 0.5s ease;
      cursor: pointer;
      border-style: none;
      @media (min-width: 1024px) {
        order: 1;
      }
      &:hover {
        background: linear-gradient(269.72deg, #8600ef 0.1%, #004eef 96.94%);
        box-shadow: 0px 32px 44px -13px rgba(34, 59, 240, 0.35);
      }
    }
    .terms {
      order: 1;
      display: flex;
      gap: 17px;
      align-items: center;
      flex: 0 0 auto;
      position: relative;
      @media (min-width: 1024px) {
        order: 2;
      }
      input[type="checkbox"] {
        flex: 0 0 auto;
        --active: #004eef;
        --active-inner: #fff;
        --focus: 2px rgba(39, 94, 254, 0.3);
        --border: transparent;
        --border-hover: #d5dbe2;
        --background: #f2f5f8;
        --disabled: #f6f8ff;
        --disabled-inner: #e1e6f9;
        -webkit-appearance: none;
        -moz-appearance: none;
        width: 21px;
        height: 21px;
        outline: none;
        display: inline-block;
        border-radius: 7px;
        vertical-align: top;
        position: relative;
        margin: 0;
        cursor: pointer;
        border: 1px solid var(--bc, var(--border));
        background: var(--b, var(--background));
        transition: background 0.3s, border-color 0.3s, box-shadow 0.2s;
      }
      input[type="checkbox"]:after {
        content: "";
        display: block;
        left: 0;
        top: 0;
        position: absolute;
        transition: transform var(--d-t, 0.3s) var(--d-t-e, ease),
          opacity var(--d-o, 0.2s);
        opacity: var(--o, 0);
        width: 5px;
        height: 9px;
        border: 2px solid var(--active-inner);
        border-top: 0;
        border-left: 0;
        left: 6px;
        top: 3px;
        transform: rotate(var(--r, 20deg));
      }
      input[type="checkbox"]:checked {
        --b: var(--active);
        --bc: var(--active);
        --d-o: 0.3s;
        --d-t: 0.6s;
        --d-t-e: cubic-bezier(0.2, 0.85, 0.32, 1.2);
        --o: 1;
        --r: 43deg;
      }
      input[type="checkbox"]:disabled {
        --b: var(--disabled);
        cursor: not-allowed;
        opacity: 0.9;
      }
      input[type="checkbox"]:hover:not(:checked) {
        --bc: var(--border-hover);
      }
      input[type="checkbox"] + label {
        font-size: 14px;
        line-height: 24px;
        font-weight: bold;
        color: #31425a;
        display: inline-block;
        vertical-align: top;
        cursor: pointer;
        margin-left: 4px;
      }
      .terms-errors {
        color: #f43c3c;
        position: absolute;
        bottom: -20px;
        left: 40px;
      }
    }
  }
}
</style>
