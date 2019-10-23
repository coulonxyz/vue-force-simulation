<template>
    <div>
        <b-button-group class="w-100">
            <b-button v-for="agent in agents" v-bind:style="getStyle(agent)"
                      v-on:click="toggle(agent)" size="sm" squared>
                {{ agent.name }}
            </b-button>
        </b-button-group>
    </div>
</template>
<script>
  export default {
    name: 'Menu',
    props: ['agents'],
    data() {
      return {};
    },
    methods: {
      toggle: function(agent) {
        agent.visible = !agent.visible;
        this.$emit('inputData', this.agents);
      },
      getStyle(agent) {

        return agent.visible ? { color: 'white', backgroundColor: agent.color } : {
          color: agent.color,
          backgroundColor: 'white'
        };
      }
    }

  };
</script>
<style scoped>
    .checked {
        color: white;
        background-color: red;
    }

    .unchecked {
        color: red;
        background-color: white;
    }
</style>
