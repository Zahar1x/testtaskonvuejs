<template>
  <div class="container mt-3">
    <h2>Форма подачи заявки в отдел сервиса и качества</h2>

    <div class="form" id="form">

      <div class="first">
        <h5>Ваш филиал <span class="reqiured">*</span> </h5>

        <div id="select">
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
          <div class="check">
            <input class="input" type="radio" name="flexRadioDefault" id="flexRadioDefault1"
                   v-model="theme">
            <label class="label" for="flexRadioDefault1" >
              {{ this.problems[0] }}
            </label>
          </div>

          <div class="check">
            <input class="input" v-model="theme" type="radio"
                   name="flexRadioDefault" id="flexRadioDefault2">
            <label class="label" for="flexRadioDefault1">
              {{ this.problems[1] }}
            </label>
          </div>

          <div class="check">
            <input class="input" v-model="theme"
                   type="radio" name="flexRadioDefault" id="flexRadioDefault3">
            <label class="label" for="flexRadioDefault1">
              {{ this.problems[2] }}
            </label>
          </div>

          <div class="check">
            <input class="input" v-model="theme" type="radio"
                   name="flexRadioDefault" id="flexRadioDefault4">
            <label class="label" for="flexRadioDefault1">
              {{ this.problems[3] }}
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

          <p class="textfileimport">Приложите, пожалуйста, полноэкранный скриншот. <br> Это поможет быстрее решить проблему.</p>
        </div>
        <div >
          <input class="fileimport" type="file" id="formFile">
        </div>

      </div>

      <div class="fifth">
        <button type="button" v-on:click="sendForm" v-bind:disabled="buttonDisable" class="btn">
          Отправить
        </button>
      </div>
    </div>

    <div >

    </div>
  </div>

</template>

<script>

import axios from "axios";
export default {
  data() {
    return {
      cities: "",
      problems: ['Недоволен качеством услуг',
        'Расторжение договора',
        'Не приходит письмо активации на почту',
        'Не работает личный кабинет'],
      endPointForGet: 'https://60254fac36244d001797bfe8.mockapi.io/api/v1/city',
      endPointForPost: 'https://60254fac36244d001797bfe8.mockapi.io/api/v1/send-form',
      selectDisabled: false,
      clickOnAnotherField: false,
      buttonDisable: false,
      errs: [],
      selecter: null,
      checkbox: null,
      theme: null,
      description: null,
      successPost: null,
      isPostSuccess: null,
    }
  },

  created() {
    this.getCities()
  },
  watch: {
    '$form'() {
      this.setButtonEnable();
    }
  },
  methods: {
    getCities() {
      axios.get(this.endPointForGet).then(res => {
        this.cities = res.data
        var arr = this.cities.map((obj) => {
          return obj.title
        })
        this.cities = arr
      }).catch(err => {
        console.log('Error' + err)
      })
    },
    setButtonEnable() {
      console.log("checkForm")
      if (this.selecter && this.description && this.theme) {
        console.log('if Passed')
        this.buttonDisable = false
      }
      else {

        console.log("All is bad")
      }
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
      console.log(this.selecter + '\t' + this.theme + '\t' + this.description)
      axios.post(this.endPointForPost).then(res => {
        this.successPost = res.data
        this.isPostSuccess = this.successPost.success
      }).catch(err => {
        console.log('Error' + err)
      })
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
.reqiured {
 color: red;
}
.select {

}
.check {
  margin-right: 1px;
  margin-top:2px;
  padding-right: 5px;
}
.label {
  padding-left: 5px;
}
.second {
  margin-top: 25px;
}
.control {
  margin-top: 5px;
}
.third {
  margin-top: 25px;
}
.textarea {
  width: 295px;
}
.fileimport {
  border: gray 1px solid;
  margin-bottom: 15px;
}
.textfileimport {
  margin-bottom: 0;
}
.btn {
  background-color: #1976d2;
  color: white;
}
</style>
