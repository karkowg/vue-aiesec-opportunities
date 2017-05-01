<template lang="html">
  <ul class="opportunity">
    <li>id: {{ opportunity.id }}</li>
    <li>title: {{ opportunity.title }}</li>
    <li>org: {{ opportunity.branch.name }}</li>
    <li>location: {{ opportunity.location }}</li>
    <li>duration: {{ opportunity.duration }} weeks</li>
    <li>status: {{ opportunity.current_status }}</li>
    <li>n-applications: {{ opportunity.applications_count }}</li>
    <li>details: <a :href="opportunityUrl" target="_blank">url</a></li>
    <li>created-at: {{ opportunity.created_at }}</li>
    <li>updated-at: {{ opportunity.updated_at }}</li>
  </ul>
</template>

<script>
export default {
  name: 'opportunity',
  props: ['opportunity'],
  data () {
    return {
      baseUrl: '//opportunities.aiesec.org/opportunity/'
    }
  },
  computed: {
    opportunityUrl () {
      return this.baseUrl + this.opportunity.id
    }
  }
}
</script>
