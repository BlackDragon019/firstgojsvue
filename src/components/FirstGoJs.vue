<template>
    <div class="gojs-map-container">
        <div ref="palette" class="gojs-palette"></div>
        <div ref="diagram" class="gojs-diagram"></div>
  </div>
</template>

<script>
    import go from 'gojs'

    export default {
        name: 'GoJSMap',
        data() {
            return {
                myDiagram: null,
            };
        },
        mounted() {
            this.initDiagram();
        },
        methods: {
            initDiagram() {
                //Crea la paleta
                const myPalette = this.$refs.palette;
                const palette = go.GraphObject.make(go.Palette, myPalette);

                //Crea el diagrama
                const myDiagram = this.$refs.diagram;
                const diagram = go.GraphObject.make(go.Diagram, myDiagram, {
                    initialContentAlignment: go.Spot.Center,
                    'undoManager.isEnabled': true,
                    // Configuración para que el diagrama se ajuste al contenedor
                    autoScale: go.Diagram.Uniform,
                    contentAlignment: go.Spot.Center,
                    //Permitir enlaces desde y hacia el diagrama
                    allowLink: true,
                    //Configurar el tipo de enlace
                    linkTemplate: go.GraphObject.make(go.Link, go.Link.Bezier, go.GraphObject.make(go.Shape, { toArrow: 'Standard' })),
                });

                //Definir la plantilla del nodo en la paleta
                palette.nodeTemplate = go.GraphObject.make(
                    go.Node,
                    'Auto',
                    go.GraphObject.make(go.Shape,'RoundedRectangle',{fill: 'lightgray'}),
                    go.GraphObject.make(go.TextBlock, { margin: 8 },new go.Binding('text', 'key'))
                );

                //Definir la plantilla del nodo en el diagrama
                diagram.nodeTemplate = go.GraphObject.make(
                    go.Node,
                    'Auto',
                    go.GraphObject.make(go.Shape, 'Circle', { fill: 'white' }),
                    go.GraphObject.make(go.TextBlock, { margin: 8 }, new go.Binding('text', 'key'))
                );

                //Enlace (conector entre nodos)
                diagram.linkTemplate = go.GraphObject.make(go.Link, go.Link.Bezier, go.GraphObject.make(go.Shape), go.GraphObject.make(go.Shape, { toArrow: 'Standard' }));
                //Agregar nodos de ejemplo
                const nodeDataArray = [
                    { key: 'Alpha' },
                    { key: 'Beta' },
                    { key: 'Gamma' },
                    { key: 'Omega' },
                    { key: 'Theta' },
                ];

                //Agregar linea entre el nodo Alpha y nodo Beta
                const linkDataArray = [
                    { from: 'Alpha', to: 'Beta'},
                    { from: 'Omega', to: 'Alpha'},
                    
                ];

                diagram.model = new go.GraphLinksModel(nodeDataArray, linkDataArray);

                //Agrega nodos a la paleta
                const paletteNodeDataArray = [
                    { key: 'Omega' },
                    { key: 'Theta' },
                    { key: 'Narciso' },
                ];

                palette.model = new go.GraphLinksModel(paletteNodeDataArray);

                this.myDiagram = diagram;
            },
        },
    };

</script>

<style scoped>
    /* Estilos personalizados para tu componente GoJSMap */
.gojs-map-container {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: flex-start;
  height: 100vh; /* 100% de la altura de la ventana */
}

.gojs-palette,
.gojs-diagram {
  flex: 1; /* Ocupa el espacio disponible de manera equitativa */
  height: 100%; /* 100% del contenedor */
  border: 1px solid black;
}

</style>