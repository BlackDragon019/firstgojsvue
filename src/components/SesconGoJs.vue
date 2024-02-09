<template>
    <div>
      <div ref="diagram" style="width: 600px; height: 400px;"></div>
      <table style="width: 200px; border: 1px solid black;">
        <thead>
          <tr>
            <th>Seleccionar</th>
            <th>Nombre</th>
            <th>Características</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in paletteItems" :key="index">
            <td>
              <input type="checkbox" v-model="item.selected">
            </td>
            <td
              @dragstart="startDrag($event, item)"
              @dragover.prevent
              @drop="dropOnDiagram($event)"
              draggable
            >
              {{ item.key }}
            </td>
            <td>{{ item.info }}</td>
          </tr>
        </tbody>
      </table>
      <button @click="addToDiagramFromButton">Agregar al Diagrama</button>
    </div>
  </template>
  
  <script>
  import go from "gojs";
  
  export default {
    data() {
      return {
        paletteItems: [
          { key: "zeta", info: "Características de Zeta", selected: false },
          { key: "tera", info: "Características de Tera", selected: false }
        ],
        diagram: null
      };
    },
    mounted() {
      this.initDiagram();
    },
    methods: {
      initDiagram() {
        const $ = go.GraphObject.make;
  
        const myDiagram = $(go.Diagram, this.$refs.diagram, {
          initialContentAlignment: go.Spot.Center,
          model: $(go.GraphLinksModel, {
            linkKeyProperty: "key",
            nodeDataArray: [
              { key: "alpha", info: "Características de Alpha", category: "alpha" },
              { key: "beta", info: "Características de Beta", category: "beta" }
            ]
          })
        });
  
        myDiagram.nodeTemplateMap.add(
          "alpha",
          $(
            go.Node,
            "Auto",
            $(go.Shape, "RoundedRectangle", { fill: "lightblue" }),
            $(go.TextBlock, { margin: 8 }, new go.Binding("text", "key"))
          )
        );
  
        myDiagram.nodeTemplateMap.add(
          "beta",
          $(
            go.Node,
            "Auto",
            $(go.Shape, "RoundedRectangle", { fill: "lightgreen" }),
            $(go.TextBlock, { margin: 8 }, new go.Binding("text", "key"))
          )
        );
  
        this.diagram = myDiagram;
      },
      startDrag($event, item) {
        if (item.selected) {
          const draggedNode = { key: item.key, info: item.info, category: "beta" };
          event.dataTransfer.setData("text/plain", JSON.stringify(draggedNode));
        }
      },
      addToDiagram(item) {
        if (item.selected && this.diagram) {
          this.diagram.model.addNodeData({ key: item.key, info: item.info, category: "beta" });
        } else if (!item.selected && this.diagram) {
          const nodeToRemove = this.diagram.findNodeForKey(item.key);
          if (nodeToRemove) {
            this.diagram.model.removeNodeData(nodeToRemove.data);
          }
        }
      },
      dropOnDiagram($event) {
        event.preventDefault();
        const data = JSON.parse(event.dataTransfer.getData("text/plain"));
        if (this.diagram) {
          this.addToDiagram(data);
        }
      },
      addToDiagramFromButton() {
        const selectedItems = this.paletteItems.filter(item => item.selected);
        selectedItems.forEach(item => {
          this.addToDiagram(item);
          // Actualiza el estado de selected después de agregar al diagrama
          item.selected = false;
        });
      }
    }
  };
  </script>
  
  <style scoped>
  
  </style>
  