<template>
  <div class="container mt-3">
    <h2>Форма подачи заявки в отдел сервиса и качества</h2>

    <div class="form" id="form">
        <div class="first">
          <h5>Ваш филиал <span class="reqiured">*</span> </h5>

          <div>
            <select v-bind:disabled="selectDisabled" class="select" v-model="selecter">
              <option selected disabled>Выберите город</option>
              <option v-for="city in this.cities" v-bind:key="city">{{city}}</option>
            </select>
          </div>
          <div class="check" id="checkbox">
            <input class="input" type="checkbox"
                   v-on:change="$emit('change', setDisabled())">
            <label class="label" for="checkbox" >Онлайн</label>
          </div>

        </div>

        <div class="second">

          <h5>Тема обращения <span class="reqiured">*</span> </h5>
          <div class="check" v-for="problem in problems" :key="problem">
            <input class="input" type="radio" name="flexRadioDefault" id="flexRadioDefault1"
                   v-model="theme" :value="problem">
            <label class="label" for="flexRadioDefault1" >
              {{ problem }}
            </label>
          </div>

          <input class="control" type="text" placeholder="Другое" v-model="theme"
                 v-on:click="clickOnfield()">
        </div>

        <div class="third">
          <div class="textarea">
            <h5>Описание проблемы <span class="reqiured">*</span></h5>
            <textarea class="textarea" id="textArea" rows="5"
                      v-model="description" placeholder="Введите текст"></textarea>
          </div>
        </div>

        <div class="forth">
          <div class="textfileimport">
            <h5>Загрузка документов</h5>

            <p>Приложите, пожалуйста, полноэкранный скриншот. <br> Это поможет быстрее решить проблему.</p>
          </div>
          <div >
            <input class="fileimport" type="file" id="formFile">
          </div>

        </div>

        <div class="fifth">
          <button type="button" v-on:click="sendForm" v-bind:disabled="isDataValidate" class="btn">
            Отправить
          </button>
        </div>
    </div>

    <modal v-show="isModalVisible" @close="closeModal"/>
  </div>
</template>

<script>

import axios from "axios";
import modal from "@/components/modal";

export default {
  components: {
    modal
  },
  data() {
    return {
      problems: ['Недоволен качеством услуг',
        'Расторжение договора',
        'Не приходит письмо активации на почту',
        'Не работает личный кабинет'],
      cities: "",
      endPointForGet: 'https://60254fac36244d001797bfe8.mockapi.io/api/v1/city',
      endPointForPost: 'https://60254fac36244d001797bfe8.mockapi.io/api/v1/send-form',
      selectDisabled: false,
      clickOnAnotherField: null,
      buttonDisable: false,
      errs: [],
      selecter: null,
      checkbox: null,
      theme: null,
      description: null,
      isPostSuccess: null,
      isModalVisible: false,
    }
  },
  created() {
    this.getCities()
  },

  computed: {
    isDataValidate() {
      return !((this.selecter || this.checkbox) && this.theme && this.description)
    }
  },
  methods: {
    showModal() {
      this.isModalVisible = true
      this.checkbox = false
      this.selecter = null
      this.description = null
      this.theme = null

    },
    closeModal() {
      this.isModalVisible = false;
    },
    getCities() {
      axios.get(this.endPointForGet).then(res => {
        this.cities = res.data.map(obj => obj.title)
      }).catch(err => {
        console.log('Error' + err)
      })
    },
    setDisabled() {
      this.selectDisabled = !this.selectDisabled
      this.selecter = this.selectDisabled
    },

    clickOnfield() {
      this.clickOnAnotherField = !this.clickOnAnotherField
    },

    sendForm() {
      console.log('trying to send post')
      axios.post(this.endPointForPost).then(res => {
        this.isPostSuccess = res.data.success
        this.resOfSending()
      }).catch(err => {
        console.log('Error' + err)
      })
    },
    resOfSending() {
      if (this.isPostSuccess) {
        this.showModal()
      } else {
        alert('Ошибка отправки заявки')
      }
    }

  }
}
</script>

<style>
.form {
  border: 1px solid gray;
  padding: 10px 20px;
  box-shadow: gray 0 0 3px;
  padding: 10px;
}
.first {
  margin-left: 10px;
}
.reqiured {
  color: red;
}
.check {
  margin-right: 1px;
  margin-top:2px;
  padding-right: 5px;
}
.label {
  margin-left: 10px;
  padding-left: 5px;
}
.second {
  margin-left: 10px;
  margin-top: 25px;
}
.control {
  margin-top: 5px;
}
.third {
  margin-left: 10px;
  margin-top: 25px;
}
.textarea {
  width: 1250px;
}
.fileimport {
  margin-left: 10px;
  border: gray 1px solid;
  margin-bottom: 15px;
}
.textfileimport {
  margin-left: 10px;
  margin-bottom: 0;
}
.btn {
  margin-left: 10px;
  background-color: #1976d2;
  color: white;
}
</style>