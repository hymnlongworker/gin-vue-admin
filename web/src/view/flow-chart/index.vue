<template>
  <div>
    <BpmnDesigner />
  </div>
</template>

<script>
import BpmnDesigner from '@/components/BpmnDesigner/index.vue';

export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: 'Workflow',
  components: {
    BpmnDesigner
  }
};
</script>
