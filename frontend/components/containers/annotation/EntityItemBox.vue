<template>
  <div>
    <div class="hl-checkbox">
      <v-btn
        small
        color="primary"
        dark
        @click="refreshEntityItemBox"
      >
      Remove Highlights
      </v-btn>
    </div>
    <div class="clearfix"></div>
    <entity-item-box
      v-if="isReady"
      :key="refreshCount"
      :labels="items"
      :text="currentDoc.text"
      :entities="currentDoc.annotations"
      :delete-annotation="removeEntity"
      :update-entity="updateEntity"
      :add-entity="addEntity"
      :refresh-entity-item-box="refreshEntityItemBox"
    />
  </div>
</template>

<script>
import { mapActions, mapGetters, mapState } from 'vuex'
import EntityItemBox from '~/components/organisms/annotation/EntityItemBox'

export default {
  components: {
    EntityItemBox
  },

  data() {
    return {
      refreshCount: 0
    }
  },

  computed: {
    ...mapState('labels', ['items', 'loading']),
    ...mapState('documents', { documentLoading: 'loading' }),
    ...mapGetters('documents', ['currentDoc']),
    isReady() {
      return !!this.currentDoc && !this.loading && !this.documentLoading
    }
  },

  created() {
    this.getLabelList({
      projectId: this.$route.params.id
    })
  },

  methods: {
    ...mapActions('labels', ['getLabelList']),
    ...mapActions('documents', ['getDocumentList', 'deleteAnnotation', 'updateAnnotation', 'addAnnotation']),
    removeEntity(annotationId) {
      const payload = {
        annotationId,
        projectId: this.$route.params.id
      }
      this.deleteAnnotation(payload)
      this.refreshEntityItemBox()
    },
    updateEntity(labelId, annotationId) {
      const payload = {
        annotationId,
        label: labelId,
        projectId: this.$route.params.id
      }
      this.updateAnnotation(payload)
    },
    addEntity(startOffset, endOffset, labelId) {
      const payload = {
        start_offset: startOffset,
        end_offset: endOffset,
        label: labelId,
        projectId: this.$route.params.id
      }
      this.addAnnotation(payload)
    },
    refreshEntityItemBox() {
      this.refreshCount = this.refreshCount + 1
    }
  }
}
</script>
<style scoped>
.hl-checkbox {
  margin-bottom: 15px;
  font-size: 14px;
  float: right;
}
.clearfix {
  clear: both;
}
</style>
