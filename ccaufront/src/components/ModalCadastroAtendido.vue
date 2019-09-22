<template>
  
</template>

<script>
export default {
  // name: 'ModalCadastroAtendido',
  data () {
    return {
      genderOptions: [
        { label: 'Masculino', value: 'male' },
        { label: 'Feminino', value: 'female' }
      ]
    }
  },
  props: [
    'name',
    'registrationNumber',
    'birthDate',
    'registrationDate',
    'gender'
  ],
  methods: {
    onSubmit () {
      console.log(this.props.name)
    //   this.$axios
    //     .post('https://localhost:5001/api/attendeds', this.attended)
    //     .then((response) => {
    //       console.log(this.attended)
    //     })
    },
    reset () {
      // this.attended = {}
    }
  }
}
</script>
