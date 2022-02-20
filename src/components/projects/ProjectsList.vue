<template>
  <base-container v-if="user">
    <h2>{{ user.fullName }}: Projects</h2>
    <base-search
      v-if="hasProjects"
      @search="updateSearch"
      :search-term="enteredSearchTerm"
    ></base-search>
    <ul v-if="hasProjects">
      <project-item
        v-for="prj in availableProjects"
        :key="prj.id"
        :title="prj.title"
      ></project-item>
    </ul>
    <h3 v-else>No projects found.</h3>
  </base-container>
  <base-container v-else>
    <h3>No user selected.</h3>
  </base-container>
</template>

<script>
import _ from 'lodash';

import { computed, ref, watch } from 'vue';

import ProjectItem from './ProjectItem.vue';

export default {
  components: {
    ProjectItem,
  },
  props: {
    user: Object,
  },
  setup(props) {
    // data
    const enteredSearchTerm = ref('');
    const activeSearchTerm = ref('');

    // computed
    const hasProjects = computed(() => !_.isEmpty(props.user.projects));

    const availableProjects = computed(() => {
      if (activeSearchTerm.value) {
        return _.filter(props.user.projects, (prj) =>
          prj.title.includes(activeSearchTerm.value)
        );
      }

      return props.user.projects;
    });

    // methods
    const updateSearch = (value) => {
      enteredSearchTerm.value = value;
    };

    // watchers
    watch(enteredSearchTerm, (val) => {
      setTimeout(() => {
        if (val === enteredSearchTerm.value) {
          activeSearchTerm.value = val;
        }
      }, 300);
    });

    watch(props.user, () => {
      enteredSearchTerm.value = '';
    });

    return {
      enteredSearchTerm,
      activeSearchTerm,
      hasProjects,
      availableProjects,
      updateSearch,
    };
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
