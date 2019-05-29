<script>
  import { getInteractive, getSession } from '../../../api/interactive'
  import draggableOrderingComponent from './draggableOrdering/index'
  import insertCodeComponent from './insertCode'
  import draggableStatementCompletionComponent from './draggableStatementCompletion'

  module.exports = Vue.extend({
    components: {
      'draggable-ordering': draggableOrderingComponent,
      'insert-code': insertCodeComponent,
      'draggable-statement-completion': draggableStatementCompletionComponent
    },

    props: {
      interactiveIdOrSlug: {
        type: String,
        required: true,
        default: ''
      },

      introLevelId: {
        type: String,
        required: true,
        default: ''
      },

      courseInstanceId: {
        type: String,
        default: undefined
      }
    },

    data: () => ({
      interactive: {},
      interactiveSession: {},
      interactiveType: ''
    }),

    async created () {
      if (!me.hasInteractiveAccess()) {
        alert('You must be logged in as an admin to use this page.')
        return application.router.navigate('/', { trigger: true })
      }

      try {
        this.interactive = await getInteractive(this.interactiveIdOrSlug)
        this.interactiveType = this.interactive.interactiveType
        if (!this.interactiveType) {
          console.error('Interactive type is not set for the interactive', this.interactiveIdOrSlug)
          noty({ text: 'Interactive type is not set for the interactive', type: 'error', timeout: '2000' })
          return
        }

        const getSessionOptions = {
          introLevelId: this.introLevelId,
          courseInstanceId: this.courseInstanceId
        }

        this.interactiveSession = await getSession(this.interactiveIdOrSlug, getSessionOptions)
      } catch (err) {
        console.error('Error:', err)
        noty({ text: 'Error occured in getting interactives data.', type: 'error', timeout: '2000' })
      }
    },

    methods: {
      onCompleted () {
        this.$emit('completed')
      }
    }
  })
</script>

<template>
  <draggable-statement-completion
   v-if="false"
   :interactive="interactive"
   :introLevelId="introLevelId"
   :interactiveSession="interactiveSession"
   :courseInstanceId="courseInstanceId"
   @completed="onCompleted"
  />

  <draggable-ordering
    v-else-if="false"
    :interactive="interactive"
    :introLevelId="introLevelId"
    :interactiveSession="interactiveSession"
    :courseInstanceId="courseInstanceId"
    @completed="onCompleted"
  />

  <insert-code
    v-else-if="true"
    :interactive="interactive"
    :introLevelId="introLevelId"
    :interactiveSession="interactiveSession"
    :courseInstanceId="courseInstanceId"
    @completed="onCompleted"
  />
</template>

<style scoped>

</style>
