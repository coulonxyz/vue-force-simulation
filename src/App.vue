<template>
    <div id="app">
        <Menu :agents="agents" @inputData="updateMessage"></Menu>
        <Summary :nodes="nodes" :links="links"></Summary>
        <force :nodes="nodes" :links="links"></force>
    </div>
</template>

<script>
  import Menu from './components/Menu.vue';
  import conversationData from './data/data.json';
  import { flatMap, isEqual, uniqWith } from 'lodash';
  import Vue from 'vue';
  import BootstrapVue from 'bootstrap-vue';
  import 'bootstrap/dist/css/bootstrap.css';
  import 'bootstrap-vue/dist/bootstrap-vue.css';
  import Summary from './components/Summary';
  import Force from './components/Force';

  Vue.use(BootstrapVue);

  export default {
    name: 'app',
    components: {
      Force,
      Menu,
      Summary
    },
    data() {
      return {
        agents: [],
        nodes: [],
        links: []
      };
    },
    created() {
      this.agents = this.getAgents();
      this.nodes = this.getNodes();
      this.links = this.getLinks();
    },
    methods: {
      getAgents() {
        return uniqWith(flatMap(conversationData.nodes.map(node => ({
          name: node.agent,
          color: node.color,
          visible: false
        }))), isEqual);
      },
      getNodes() {
        return conversationData.nodes.filter(node => this.getVisibleAgents().includes(node.agent));
      },
      getLinks() {
        return conversationData.links
          .filter(link => {
            return this.getVisibleAgents().includes(link.sourceAgent) && this.getVisibleAgents().includes(link.targetAgent);
          })
      },
      getVisibleAgents() {
        return this.agents.filter(agent => agent.visible).map(agent => agent.name);
      },
      updateMessage() {
        this.nodes = this.getNodes();
        this.links = this.getLinks();
      }
    }
  };
</script>

<style>
</style>
