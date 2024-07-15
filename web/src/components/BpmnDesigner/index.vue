<template>
  <div>
    <div id="bpmn-container" ref="bpmnContainer"></div>
    <div style="margin-top: 10px;">
      <button @click="exportDiagram">Export BPMN</button>
      <input type="file" @change="handleFileUpload" />
    </div>
  </div>
</template>

<script>
import BpmnModeler from 'bpmn-js/lib/Modeler';
import 'bpmn-js/dist/assets/diagram-js.css';
import 'bpmn-js/dist/assets/bpmn-font/css/bpmn-embedded.css';

export default {
  name: 'BpmnDesigner',
  data() {
    return {
      modeler: null,
      initialDiagram: `<?xml version="1.0" encoding="UTF-8"?>
        <bpmn:definitions xmlns:bpmn="http://www.omg.org/spec/BPMN/20100524/MODEL"
                          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                          id="Definitions_1"
                          targetNamespace="http://bpmn.io/schema/bpmn">
          <bpmn:process id="Process_1" isExecutable="false">
            <bpmn:startEvent id="StartEvent_1" />
          </bpmn:process>
          <bpmndi:BPMNDiagram id="BPMNDiagram_1">
            <bpmndi:BPMNPlane id="BPMNPlane_1" bpmnElement="Process_1">
              <bpmndi:BPMNShape id="BPMNShape_StartEvent_2" bpmnElement="StartEvent_1">
                <dc:Bounds x="179" y="79" width="36" height="36" />
              </bpmndi:BPMNShape>
            </bpmndi:BPMNPlane>
          </bpmndi:BPMNDiagram>
        </bpmn:definitions>`
    };
  },
  mounted() {
    this.initializeBpmnModeler();
  },
  methods: {
    initializeBpmnModeler() {
      this.modeler = new BpmnModeler({
        container: this.$refs.bpmnContainer,
        width: '100%',
        height: '600px'
      });

      this.importDiagram(this.initialDiagram);
    },
    importDiagram(xml) {
      this.modeler.importXML(xml, (err) => {
        if (err) {
          console.error('Failed to import diagram:', err);
          alert('Failed to import diagram: ' + err.message);
        } else {
          const canvas = this.modeler.get('canvas');
          canvas.zoom('fit-viewport');
        }
      });
    },
    exportDiagram() {
      this.modeler.saveXML({format: true}, (err, xml) => {
        if (err) {
          console.error('Failed to save diagram:', err);
          alert('Failed to save diagram: ' + err.message);
        } else {
          console.log('BPMN XML:', xml);
          this.downloadXML(xml);
        }
      });
    },
    handleFileUpload(event) {
      const file = event.target.files[0];
      const reader = new FileReader();

      reader.onload = (e) => {
        const xml = e.target.result;
        this.importDiagram(xml);
      };

      reader.readAsText(file);
    },
    downloadXML(xml) {
      const blob = new Blob([xml], {type: 'application/xml'});
      const url = URL.createObjectURL(blob);
      const a = document.createElement('a');
      a.href = url;
      a.download = 'diagram.bpmn';
      a.click();
      URL.revokeObjectURL(url);
    }
  }
};
</script>

<style scoped>
#bpmn-container {
  width: 100%;
  height: 600px;
  border: 1px solid #ccc;
}
</style>
