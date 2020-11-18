<template>
  <div class="contact">
    <v-row class="text-center pa-3">
      <v-col
        cols="12"
      >
        <h3>お問い合わせ</h3>
        <hr width="100%" color="#50B197" align="center" noshade>
        <v-form
          v-model="valid"
        >
          <v-textarea
            v-model="content.value"
            :rules="content.rules"
            :label="content.label"
          />
          <v-text-field
            v-model="companyname.value"
            :rules="companyname.rules"
            :label="companyname.label"
          />
          <v-text-field
            v-model="departmentname.value"
            :rules="departmentname.rules"
            :label="departmentname.label"
          />
          <v-text-field
            v-model="fullname.value"
            :rules="fullname.rules"
            :label="fullname.label"
          />
          <v-text-field
            v-model="fullnamekana.value"
            :rules="fullnamekana.rules"
            :label="fullnamekana.label"
          />
           <v-text-field
            v-model="contact.value"
            :rules="contact.rules"
            :label="contact.label"
          />
           <v-text-field
            v-model="phone.value"
            :label="phone.label"
          />
          <v-btn
            block
            color="success"
            class="font-weight-bold"
            @click="dialog = true"
          >
            入力内容の確認
          </v-btn>
        </v-form>
      </v-col>
    </v-row>

    <v-dialog
      v-model="dialog"
      max-width="500"
    >
      <v-card
        v-if="valid"
        class="pa-3"
      >
        <v-card-text
          class="pt-5"
        >
          <h4>
            {{ companyname.label }}
          </h4>
          <p>
            {{ companyname.value }}
          </p>
          <h4>
            {{ departmentname.label }}
          </h4>
          <p>
            {{ departmentname.value }}
          </p>
          <h4>
            {{ fullname.label }}
          </h4>
          <p>
            {{ fullname.value }}
          </p>
          <h4>
            {{ fullnamekana.label }}
          </h4>
          <p>
            {{ fullnamekana.value }}
          </p>
          <h4>
            {{ contact.label }}
          </h4>
          <p>
            {{ contact.value }}
          </p>
          <h4>
            {{ phone.label }}
          </h4>
          <p>
            {{ phone.value }}
          </p>
          <h4>
            {{ content.label }}
          </h4>
          <p
            class="
              breakLine
              contentPreview
            "
          >
            {{ content.value }}
          </p>
        </v-card-text>
        <v-btn
          class="warning"
          @click="dialog = false"
        >
          戻る
        </v-btn>
        <v-btn
          class="success float-right"
          @click="post(); dialog = false"
        >
          送信
        </v-btn>
      </v-card>
      <v-card
        v-if="!valid"
        class="pa-3"
      >
        <v-card-text
          class="pt-5"
        >
          お問い合わせ内容を正しく入力してください。
        </v-card-text>
        <v-btn
          class="warning"
          @click="dialog = false"
        >
          戻る
        </v-btn>
      </v-card>
    </v-dialog>

    <v-overlay
      :value="loading"
    >
      <v-progress-circular
        indeterminate
        :size="80"
        :width="10"
      />
    </v-overlay>

    <v-snackbar
      v-model="snackbar"
      top
    >
      {{ message }}
      <template v-slot:action="{ attrs }">
        <v-btn
          class="warning"
          text
          v-bind="attrs"
          @click="snackbar = false"
        >
          閉じる
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>
<script>
import axios from 'axios'

export default {
  name: 'Contact',
  data () {
    return {
      content: {
        value: null,
        rules: [v => !!v || '※必須'],
        label: 'お問い合わせ内容'
      },
      companyname: {
        value: null,
        rules: [v => !!v || '※必須'],
        label: '企業名・団体名'
      },
      departmentname: {
        value: null,
        rules: [v => !!v || '※必須'],
        label: '部署名'
      },
      fullname: {
        value: null,
        rules: [v => !!v || '※必須'],
        label: '氏名'
      },
      fullnamekana: {
        value: null,
        rules: [v => !!v || '※必須'],
        label: 'フリガナ'
      },
      contact: {
        value: null,
        rules: [
          v => !!v || '※必須',
          v => /.+@.+/.test(v) || 'メールアドレスの形式が正しくありません'
        ],
        label: 'メールアドレス'
      },
      phone: {
        value: null,
        label: '電話番号'
      },
     valid: false,
      dialog: false,
      snackbar: false,
      loading: false,
      message: null,
    }
  },
  methods: {
    post () {
      this.loading = true
      if (!this.valid) { return }
      const instance = axios.create({
        baseURL: 'https://0a8sxjmiod.execute-api.ap-northeast-1.amazonaws.com'
      })
      instance.post('/default/contactFunction', {
        content: this.content.value,
        companyname: this.companyname.value,
        departmentname: this.departmentname.value,
        fullname: this.fullname.value,
        fullnamekana: this.fullnamekana.value,
        contact: this.contact.value,
        phone: this.phone.value
      }).then((response) => {
        this.result = response.data.body
        this.message = 'お問い合わせ内容を送信いたしました。'
      }).catch((error) => {
        this.result = error
        this.message = '送信が失敗しました。もう一度お試しください。'
      }).finally(() => {
        this.loading = false
        this.snackbar = true
      })
    }
  }
}
</script>
<style scoped>
.breakLine {
  white-space: pre-line;
}
.contentPreview {
  max-height: 300px;
  overflow: scroll;
}
</style>
