<template>
  <div class="container">
    <h2 class="title">On discute d’ecoCode ?</h2>
    <form @submit.prevent="submitForm">
      <div class="container-form">
        <div class="box">
          <span class="title-box">Je suis :</span>
          <div><input type="radio" v-model="type" value="individu" /> Un individu</div>
          <div><input type="radio" v-model="type" value="organisation" /> Une
            organisation</div>
        </div>
        <div class="box">
          <span class="title-box">Je souhaite :</span>
          <select v-model="subject">
            <option v-for="option in options" :key="option">{{ option }}</option>
          </select>
        </div>
        <div class="box-column">
          <input class="input" v-model="name" type="text" placeholder="Nom de l’entreprise / personne" />
          <div class="merge-input">
            <input class="input" v-model="email" type="mail" placeholder="E-mail" />
            <input class="input" v-model="phone" type="text" placeholder="Téléphone" />
          </div>
          <input class="input" v-model="message" type="text" placeholder="Des éléments supplémentaires ?" />

          <div class="hcaptcha">
            <vue-hcaptcha @verify="getCaptcha" sitekey="359a430d-a0bf-4548-a583-959e93110b6d"></vue-hcaptcha>
          </div>
        </div>
      </div>
      <div class="error-message" v-if="error">{{ error }}</div>
      <div style="margin-top: 15px;" class="success-message" v-if="success">{{ success }}</div>
      <div class="container-button">
        <button type="submit" class="button">
          <img src="@/assets/img/icon/arrow-left-white.webp" />
          Envoyer
          <img src="@/assets/img/icon/arrow-right-white.webp" />
        </button>
      </div>
    </form>
  </div>
</template>

<script lang="ts" setup>
import axios from 'axios';
import { ref, watch } from "vue";
import VueHcaptcha from '@hcaptcha/vue3-hcaptcha';

let captcha = ref("");
const error = ref("");
const name = ref("");
const email = ref("");
const phone = ref("");
const message = ref("");
const success = ref("");

const validateName = () => {
  if (!name.value) {
    error.value = 'Le nom est requis.';
  } else {
    error.value = '';
    return true;
  }
};

const validateEmail = () => {
  const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
  if (!email.value) {
    error.value = 'L\'e-mail est requis.';

  } else if (!emailPattern.test(email.value)) {
    error.value = 'L\'e-mail n\'est pas valide.';
  } else {
    error.value = '';
    return true
  }
};

const validatePhone = () => {
  const phonePattern = /^\d+$/;
  if (!phone.value) {
    error.value = 'Le téléphone est requis.';
  } else if (!phonePattern.test(phone.value)) {
    error.value = 'Le téléphone doit contenir uniquement des chiffres.';
  } else if (phone.value.length < 10 || phone.value.length > 10) {
    error.value = 'Le numéro de téléphone doit contenir 10 chiffres.';
  } else {
    error.value = '';
    return true;
  }
};

const validateMessage = () => {
  if (!message.value) {
    error.value = 'Veuillez fournir des détails supplémentaires si nécessaire.';
  } else {
    error.value = '';
    return true;
  }
};

const validateCaptcha = () => {
  if (!captcha.value) {
    error.value = 'Le captcha est requis.';
  } else {
    error.value = '';
    return true
  }
};

const validateForm = () => {
  if (validateName()) {
    if (validateEmail()) {
      if (validatePhone()) {
        if (validateMessage()) {
          if (validateCaptcha()) {
            return true
          }
        }
      }
    }
  }
  if (error.value || error.value || error.value || error.value) {
    return false;
  }
  error.value = '';
  return true;
}

const submitForm = async () => {
  if (validateForm()) {
    const formData = {
      type: type.value,
      subject: subject.value,
      name: name.value,
      email: email.value,
      phone: phone.value,
      message: message.value,
      captcha: captcha.value
    };
    const headers = {
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
      }
    };
    try {
      const response = await axios.post('https://api.ecocode.io/contact', formData, headers);
      success.value = "Votre demande a bien été enregistrer";
    } catch (err) {
      error.value = "Erreur d'envoie, veuillez réessayer plus tard.";
    }
  }
};

function getCaptcha(response: any) {
  captcha.value = response;
};

const type = ref("individu");
const subject = ref("Je souhaite contribuer à la création de règles sur ecoCode");
const options = ref([
  "Je souhaite contribuer à la création de règles sur ecoCode",
  "Je souhaite m’impliquer sur d’autres aspects d’ecoCode",
  "Je souhaite des informations sur ecoCode",
]);

watch(type, (newValue) => {
  if (newValue === "individu") {
    options.value = [
      "Je souhaite contribuer à la création de règles sur ecoCode",
      "Je souhaite m’impliquer sur d’autres aspects d’ecoCode",
      "Je souhaite des informations sur ecoCode",
    ];
    subject.value = "Je souhaite contribuer à la création de règles sur ecoCode";
  } else if (newValue === "organisation") {
    options.value = [
      "Je souhaite développer des règles pour mon organisation",
      "Je souhaite contribuer à ecoCode (mise à contribution de collaborateurs)",
      "Je souhaite soutenir ecoCode financièrement",
      "Je souhaite des informations sur ecoCode",
    ];
    subject.value = "Je souhaite développer des règles pour mon organisation";
  }
});

</script>

<style scoped lang="scss">
.container {
  width: 100%;
  padding-top: 50px;
  min-height: 396px;
  height: max-content;
  z-index: 2;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: flex-start;
  background-color: #f3f3f3;
}

.container-button {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 0px 0px 40px 120px;
}

.title {
  max-width: 1089px;
  font-size: 40px;
  font-weight: 900;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.25;
  letter-spacing: normal;
  text-align: center;
  color: #355086;
  padding: 0px 0 0 0;
}

.container-form {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  flex-wrap: wrap;
}

.title-box {
  font-size: 18px;
  font-weight: 800;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.56;
  letter-spacing: 0.21px;
  color: #355086;
  padding: 0 10px 0 0;
}

.box {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  padding: 31px 0 0 0;
  flex-wrap: wrap;
}

.box-column {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  justify-content: center;
  padding: 40px 0 0 0;
  flex-wrap: wrap;
  width: 100%;
}

select {
  padding: 15px 50px 15px 25px;
  border-radius: 8px;
  border: solid 1px rgba(0, 0, 0, 0.2);
  font-size: 18px;
  font-weight: 500;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.56;
  letter-spacing: 0.21px;
  color: #022826;
  appearance: none;
  background-image: url("data:image/svg+xml;charset=US-ASCII,%3Csvg%20xmlns%3D%22http%3A%2F%2Fwww.w3.org%2F2000%2Fsvg%22%20width%3D%22292.4%22%20height%3D%22292.4%22%3E%3Cpath%20fill%3D%22%23131313%22%20d%3D%22M287%2069.4a17.6%2017.6%200%200%200-13-5.4H18.4c-5%200-9.3%201.8-12.9%205.4A17.6%2017.6%200%200%200%200%2082.2c0%205%201.8%209.3%205.4%2012.9l128%20127.9c3.6%203.6%207.8%205.4%2012.8%205.4s9.2-1.8%2012.8-5.4L287%2095c3.5-3.5%205.4-7.8%205.4-12.8%200-5-1.9-9.2-5.5-12.8z%22%2F%3E%3C%2Fsvg%3E");
  background-repeat: no-repeat;
  background-position: right 30px top 50%;
  background-size: 0.65rem auto;
}

input[type="radio"] {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  display: inline-block;
  width: 20px;
  height: 20px;
  padding: 2px;
  background-clip: content-box;
  border: 2px solid #c1d8cfff;
  background-color: #e7e6e7;
  border-radius: 50%;
  font-size: 18px;
  font-weight: 500;
  font-stretch: normal;
  font-style: normal;
  line-height: 1.56;
  letter-spacing: 0.21px;
  color: #022826;
  margin: 0 10px 0 20px;
}

input[type="radio"]:checked {
  width: 20px;
  height: 20px;
  border: 2px solid #57c18b;
  background-color: #57c18b;
}

.input {
  width: calc(100% - 120px);
  height: 60px;
  margin: 0px 0px 40px 120px;
  background-color: #ffffff;
  padding: 15px 42.5px 17px 20px;
  border-radius: 8px;
  border: solid 1px rgba(0, 0, 0, 0.2);
  color: #022826;
  font-size: 18px;
  font-weight: 500;
  outline: none;
}

.error-message {
  margin: 15px 0px 0 120px;
}

.success-message {
  margin: 15px 0px 0 120px;
}

.input:last-child {
  margin-bottom: 20px;
}

.merge-input {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  width: 100%;
}

.merge-input>.input {
  width: 100%;
  padding: 15px 33.4px 17px 20px;
}

.merge-input>.input:last-child {
  margin-left: 40px;
}

.button {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: center;
  padding: 8.4px 13px 10px;
  border-radius: 12px;
  text-align: center;
  font-size: 20px;
  font-weight: bold;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: 0.24px;
  text-align: center;
  margin: 0 auto 0 auto;
  background-color: #355086;
  border: 2px solid white;
  color: white;

  &:hover {
    cursor: pointer;
  }

  >img {
    width: 24px;
    height: auto;
  }
}

.hcaptcha {
  margin: 0px 0px 20px 120px;
}

@media screen and (max-width: 768px) {
  .title {
    padding: 0 50px 0 50px;
    font-size: 32px;
    margin: 0 36px 0 36px;
  }

  .box {
    flex-direction: column;
    align-items: flex-start;
    padding: 0 25px 20px 10px;

    >div {
      display: flex;
      flex-direction: row;
      align-items: center;
      justify-content: center;
      margin: 10px 25px 0 25px;
    }
  }

  .box-column {
    padding: 0 25px 0 10px;
  }

  .merge-input {
    width: 80%;
    margin: 0px 25px 0px 25px;
    flex-direction: column;
  }

  .merge-input>.input:last-child {
    margin-left: inherit;
  }

  input[type=radio] {
    margin: 0 10px 0 0;
  }

  .input {
    width: 80%;
    margin: 0px 25px 20px 25px;
  }

  .error-message {
    margin: 0px 0px 20px 53px;
  }

  .container-button {
    width: 80%;
    margin-left: 0px;
  }

  .title {
    padding: 0 50px 0 50px;
    font-size: 25px;
    margin: 0 36px 0 36px;
  }

  select {
    width: 80%;
    margin: 0 25px 0 25px;
  }

  .title-box {
    margin: 0 25px 0 25px;
  }

  .hcaptcha {
    margin: 0 25px 10px 25px;
  }
}
</style>
