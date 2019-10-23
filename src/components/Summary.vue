<template>

    <div>
        <p>nodes: {{ nodes.length }}</p>
        <p>links: {{ links.length }}</p>
    </div>


</template>
<script>
  export default {
    name: 'Summary',
    props: ['nodes', 'links'],
    data() {
      return {  };
    },
    methods: {}

  };
</script>
<style scoped>

</style>
