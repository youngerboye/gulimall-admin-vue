<template>
  <div>
  </div>
</template>

<script>
  export default {
    data () {
      return {
        data: [],
      }
    },
    created () {
      this.getMenu()
    },
    methods: {
      getMenu () {
        this.$http({
          url: this.$http.adornUrl('/product/pmscategory/list/tree'),
          method: 'get',
          params: null
        }).then(({data}) => {
          console.log(data)
        })
      }
    }
  }
</script>
