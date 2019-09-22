<template>
  <div class="q-pa-md">
    <div class="q-pa-md">
      <q-table
        title="Atendidos"
        :data="attendeds"
        :columns="columns"
        row-key="registrationNumber"
        selection="multiple"
        :selected.sync="selected"
      >
      </q-table>
    </div>
    <q-dialog v-model="modalRegister" persistent>
      <div class="q-pa-md" style="width: 600px">
        <div class="row"></div>
        <q-card>
          <q-card-section>
            <q-input
              v-model="attended.name"
              label="Nome Completo"
              :error="$v.attended.name.$error"
            />
            <q-input
              v-model="attended.registrationNumber"
              label="Matrícula"
              :error="$v.attended.registrationNumber.$error"
            />
            <q-datetime-picker
              v-model="attended.birthDate"
              label="Data Nascimento"
              auto-update-value lang>
            </q-datetime-picker>
            <q-datetime-picker
              v-model="attended.registrationDate"
              label="Data Matrícula"
              auto-update-value
              lang="pt-BR">
            </q-datetime-picker>
            <q-field label="Sexo" borderless>
              <q-option-group
                class="q-pt-sm"
                v-model="attended.gender"
                :options="genderOptions"
                color="primary"
                inline
              />
            </q-field>
          </q-card-section>
          <q-card-actions align="right">
            <q-btn flat label="Cancelar" color="negative" @click="reset()" v-close-popup />
            <q-btn flat label="Enviar" color="primary" @click="onSubmit()" />
          </q-card-actions>
        </q-card>
      </div>
    </q-dialog>
  </div>
</template>

<script>

import moment from 'moment'
import { required, integer } from 'vuelidate/lib/validators'
export default {
  name: 'AtendidosPage',
  data () {
    return {
      attended: {
        name: undefined,
        registrationNumber: null,
        birthDate: undefined,
        registrationDate: undefined,
        gender: undefined
      },
      genderOptions: [
        { label: 'Masculino', value: 'male' },
        { label: 'Feminino', value: 'female' }
      ],
      modalRegister: false,
      filter: '',
      loading: true,
      selection: 'multiple',
      selected: [],
      columns: [
        { name: 'registrationNumber', label: 'Matrícula', field: 'registrationNumber', sortable: true, align: 'left' },
        { name: 'name', label: 'Nome', field: 'name', sortable: true, align: 'left' },
        { name: 'birthDate', label: 'Data de Nascimento', field: 'birthDate', sortable: true, align: 'left' },
        { name: 'registrationDate', label: 'Data de Matrícula', field: 'registrationDate', sortable: true, align: 'left' },
        { name: 'gender', label: 'Sexo', field: 'gender', sortable: true, align: 'left' }
      ],
      attendeds: []
    }
  },
  validations: {
    attended: {
      name: { required },
      registrationNumber: { required, integer }
    }
  },
  mounted () {
    this.getAttendeds()
  },
  methods: {
    getAttendeds () {
      this.loading = true
      this.$axios
        .get('https://localhost:5001/api/attendeds')
        .then((response) => {
          this.attendeds = response.data
          let count = 0
          this.attendeds.forEach(element => {
            this.attendeds[count].birthDate = moment(this.attendeds[count].birthDate).format('DD/MM/YYYY')
            this.attendeds[count].registrationDate = moment(this.attendeds[count].registrationDate).format('DD/MM/YYYY')
            this.attendeds[count].gender = this.attendeds[count].gender === 0 ? 'Masculino' : 'Feminino'
            count++
          })
          this.loading = false
        })
        .catch((err) => {
          this.$q.notify({
            message: 'FALHA AO CARREGAR DADOS',
            color: 'orange',
            icon: 'error_outline'
          })
          throw new Error(err)
        })
    },
    onSubmit () {
      this.$v.$touch()
      if (!this.$v.$error) {
        this.$q.loading.show({
          delay: 200,
          spinner: 'QSpinnerPie'
        })
        this.$axios
          .post('https://localhost:5001/api/attendeds', this.attended)
          .then((response) => {
            this.$q.notify({
              message: 'CADASTRADO COM SUCESSO!',
              color: 'green',
              icon: 'done'
            })
            this.$q.loading.hide()
            this.closeModal()
            this.reset()
          })
          .catch((err) => {
            throw new Error(err)
          })
      } else {
        this.$q.notify({
          message: 'PREENCHA OS CAMPOS CORRETAMENTE',
          color: 'red-10',
          icon: 'error_outline'
        })
      }
    },
    reset () {
      this.attended = {}
      this.attended.registrationNumber = null
      this.$v.$reset()
    },
    openModal () {
      this.modalRegister = true
    },
    closeModal () {
      this.modalRegister = false
    }
  }
}
</script>
