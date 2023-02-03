<template>
  
  <div id="graph">
    <h1>Data App</h1>
      <div id="legend"></div>
  </div>

</template>


<script>
import * as d3 from "d3";

export default {
  name: "GraphView",
  data() {
    // return {
    //   nodes: [],
    //   links: []
    // };
  },
  mounted() {
    // Get the width and height of the container
    // const width = this.$el.clientWidth;
    // const height = this.$el.clientHeight;
   const width = 900;
const height = 700;

    // Fetch the data from the data.json file
    fetch("data.json")
      .then(response => response.json())
      .then(data => {
        this.nodes = data.nodes;
        this.links = data.links;

        // Create the SVG element
        const svg = d3
          .select("#graph")
          .append("svg")
          .attr("width", width)
          .attr("height", height);

        // Create the force simulation
        const simulation = d3
          .forceSimulation()
          .force("link", d3.forceLink().id(d => d.id))
          .force("charge", d3.forceManyBody())
          .force("center", d3.forceCenter(width / 2, height / 2));

        // Add the nodes and links to the simulation
        simulation.nodes(this.nodes);
        simulation.force("link").links(this.links);

        // Create the link elements
        const link = svg
          .append("g")
          .attr("class", "links")
          .selectAll("line")
          .data(this.links)
          .enter()
          .append("line")
          .attr("stroke", "#999")
          .attr("stroke-opacity", 0.6);

        // Create the node elements
        const node = svg
          .append("g")
          .attr("class", "nodes")
          .selectAll("circle")
          .data(this.nodes)
          .enter()
          .append("circle")
          .attr("r", 5)
          .attr("fill", "#fff")
          .attr("stroke", "#000")
          .call(
            d3
              .drag()
              .on("start", dragstarted)
              .on("drag", dragged)
              .on("end", dragended)
          );
        // Add on click function to node element that navigates to https://github.com/{node name}
        node.on("click", function(d) {
          window.open(`https://github.com/${d.id}`);
        });

        // Update the position of the elements as the simulation runs
        simulation.on("tick", () => {
          link
            .attr("x1", d => d.source.x)
            .attr("y1", d => d.source.y)
.attr("x2", d => d.target.x)
.attr("y2", d => d.target.y);

      node.attr("cx", d => d.x).attr("cy", d => d.y);
    });

    function dragstarted(d) {
      if (!d3.event.active) simulation.alphaTarget(0.3).restart();
      d.fx = d.x;
      d.fy = d.y;
    }

    function dragged(d) {
      d.fx = d3.event.x;
      d.fy = d3.event.y;
    }

    function dragended(d) {
      if (!d3.event.active) simulation.alphaTarget(0);
      d.fx = null;
      d.fy = null;
    }
  });
}
};
</script>


<style scoped>

/* Style the legend container */
#legend {
  position: absolute;
  bottom: 10px;
  right: 10px;
  display: flex;
  flex-wrap: wrap;
}

/* Style the legend items */
.legend-item {
  margin: 5px;
  display: flex;
  align-items: center;
}

/* Style the square color indicator */
.legend-color-square {
  width: 20px;
  height: 20px;
  margin-right: 10px;
}

/* Style the text of the legend item */
.legend-text {
  font-size: 14px;
}

/* Style the links */
.links line {
  stroke: #999;
  stroke-opacity: 0.6;
}

/* Style the nodes */
.nodes circle {
  stroke: #000;
  stroke-width: 1.5px;
  fill: #fff;
}

</style>

