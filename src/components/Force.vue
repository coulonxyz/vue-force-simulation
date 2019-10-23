<template>
    <div>
        <svg></svg>
    </div>
</template>
<script>
  import * as d3 from 'd3';

  export default {
    props: ['nodes', 'links'],
    data() {
      return {
        force: null
      };
    },
    watch: {
      nodes() {
        this.renderForce();
      }
    },
    methods: {
      renderForce() {
        const WIDTH = 800;
        const HEIGHT = 800;
        const RADIUS = 20;

        d3.selectAll('circle').remove();
        d3.selectAll('line').remove();

        const mySvg = d3
          .select('svg')
          .attr('width', WIDTH)
          .attr('height', HEIGHT);

        const simulation = d3
          .forceSimulation()
          .nodes(this.nodes);

        const link_force = d3
          .forceLink(this.links)
          .distance(d => {
            const baseDistance = d.sourceAgent === d.targetAgent ? 0 : 1000;
            return RADIUS + +baseDistance + d.occurrences * 80;
          })
          .id(d => d.name);

        const charge_force = d3
          .forceManyBody()
          .strength(-100);

        const center_force = d3
          .forceCenter(WIDTH / 2, HEIGHT / 2);

        simulation
          .force('center_force', center_force)
          .force('charge_force', charge_force)
          .force('links', link_force);

        simulation.on('tick', tickActions);

        const g = mySvg.append('g')
          .attr('class', 'everything');

        const link = g.append('g')
          .attr('class', 'links')
          .selectAll('line')
          .data(this.links)
          .enter().append('line')
          .attr('stroke-width', (d) => d.occurences)
          .style('stroke', 'black');

        const node = g.append('g')
          .attr('class', 'nodes')
          .selectAll('circle')
          .data(this.nodes)
          .enter()
          .append('circle')
          .attr('r', RADIUS)
          .attr('fill', d => d.color)
          .style('opacity', 0.8)
          .on('click', function(d) {
            console.log(d.name);
          });

        const drag_handler = d3.drag()
          .on('start', drag_start)
          .on('drag', drag_drag)
          .on('end', drag_end);

        drag_handler(node);

        const zoom_handler = d3.zoom()
          .on('zoom', zoom_actions);

        zoom_handler(mySvg);

        // Basic tooltips
        node.append('title')
          .text(function(d) {
            return d.name;
          });

        /** Functions **/
        function drag_start(d) {
          if (!d3.event.active) simulation.alphaTarget(0.3).restart();
          d.fx = d.x;
          d.fy = d.y;
        }


        function drag_drag(d) {
          d.fx = d3.event.x;
          d.fy = d3.event.y;
        }

        function drag_end(d) {
          if (!d3.event.active) simulation.alphaTarget(0);
          d.fx = null;
          d.fy = null;
        }


        function zoom_actions() {
          g.attr('transform', d3.event.transform);
        }

        function tickActions() {
          // 'update circle positions each tick of the simulation'
          node
            .attr('cx', function(d) {
              return d.x;
            })
            .attr('cy', function(d) {
              return d.y;
            });

          // 'update link positions'
          link
            .attr('x1', function(d) {
              return d.source.x;
            })
            .attr('y1', function(d) {
              return d.source.y;
            })
            .attr('x2', function(d) {
              return d.target.x;
            })
            .attr('y2', function(d) {
              return d.target.y;
            });
        }
      }

    }
  };

</script>
