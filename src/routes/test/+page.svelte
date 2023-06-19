<script>
	import * as d3 from 'd3';
	import { onMount } from 'svelte';

	// function blogGraph(graph) {
	// 	const width = '960';
	// 	const height = '600';

	// 	const sourceRadius = 45;
	// 	const entityRadius = 35;

	// 	var svg = d3.select('#networkGraph').append('svg').attr('width', width).attr('height', height);

	// 	var simulation = d3
	// 		.forceSimulation()
	// 		.force(
	// 			'link',
	// 			d3.forceLink().id(function (d) {
	// 				return d.id;
	// 			})
	// 		)
	// 		.force('charge', d3.forceManyBody().strength(-1900).theta(0.5).distanceMax(1500))
	// 		.force(
	// 			'collision',
	// 			d3.forceCollide().radius(function (d) {
	// 				return d.radius;
	// 			})
	// 		)
	// 		.force(
	// 			'center',
	// 			d3.forceCenter(
	// 				document.querySelector('#networkGraph').clientWidth / 2,
	// 				document.querySelector('#networkGraph').clientHeight / 2
	// 			)
	// 		);

	// 	var defs = svg.append('defs');

	// 	defs
	// 		.append('radialGradient')
	// 		.attr('id', 'entity-gradient')
	// 		.attr('cx', '50%')
	// 		.attr('cy', '50%')
	// 		.attr('r', '50%')
	// 		.selectAll('stop')
	// 		.data([
	// 			{ offset: '50%', color: '#ffffff' },
	// 			{ offset: '100%', color: '#CCCCCC' }
	// 		])
	// 		.enter()
	// 		.append('stop')
	// 		.attr('offset', function (d) {
	// 			return d.offset;
	// 		})
	// 		.attr('stop-color', function (d) {
	// 			return d.color;
	// 		});

	// 	defs
	// 		.append('radialGradient')
	// 		.attr('id', 'source-gradient')
	// 		.selectAll('stop')
	// 		.data([
	// 			{ offset: '20%', color: '#eda515' },
	// 			{ offset: '100%', color: '#827777' }
	// 		])
	// 		.enter()
	// 		.append('stop')
	// 		.attr('offset', function (d) {
	// 			return d.offset;
	// 		})
	// 		.attr('stop-color', function (d) {
	// 			return d.color;
	// 		});

	// 	var link = svg.append('g').selectAll('line').data(graph.links).enter().append('line');

	// 	link.style('stroke', '#aaa');

	// 	var node = svg
	// 		.append('g')
	// 		.attr('class', 'nodes')
	// 		.selectAll('circle')
	// 		.data(graph.nodes)
	// 		.enter()
	// 		.append('circle')
	// 		//I made the article/source nodes larger than attribute nodes
	// 		.attr('r', function (d) {
	// 			return d.category == 0 ? sourceRadius : entityRadius;
	// 		});

	// 	node
	// 		.style('fill', '#cccccc')
	// 		.style('fill-opacity', '0.9')
	// 		.style('stroke', '#424242')
	// 		.style('stroke-width', '1px');

	// 	node.style('fill', function (d) {
	// 		return d.category == 0 ? 'url(#source-gradient)' : 'url(#entity-gradient)';
	// 	});

	// 	var label = svg
	// 		.append('g')
	// 		.attr('class', 'labels')
	// 		.selectAll('text')
	// 		.data(graph.nodes)
	// 		.enter()
	// 		.append('text')
	// 		.text(function (d) {
	// 			return d.name;
	// 		})
	// 		.attr('class', 'label');

	// 	label.style('text-anchor', 'middle').style('font-size', function (d) {
	// 		return d.category == 1
	// 			? Math.min(2 * entityRadius, ((2 * entityRadius - 8) / this.getComputedTextLength()) * 15) +
	// 					'px'
	// 			: Math.min(2 * sourceRadius, ((2 * sourceRadius - 8) / this.getComputedTextLength()) * 15) +
	// 					'px';
	// 	});

	// 	label
	// 		.on('mouseover', function (d) {
	// 			tooltip.html(`${d.name}`);
	// 			return tooltip.style('visibility', 'visible');
	// 		})
	// 		.on('mousemove', function () {
	// 			return tooltip
	// 				.style('top', d3.event.pageY - 10 + 'px')
	// 				.style('left', d3.event.pageX + 10 + 'px');
	// 		});

	// 	node
	// 		.on('mouseover', function (d) {
	// 			tooltip.html(`${d.name}`);
	// 			return tooltip.style('visibility', 'visible');
	// 		})
	// 		.on('mousemove', function () {
	// 			return tooltip
	// 				.style('top', d3.event.pageY - 10 + 'px')
	// 				.style('left', d3.event.pageX + 10 + 'px');
	// 		})
	// 		.on('mouseout', function () {
	// 			return tooltip.style('visibility', 'hidden');
	// 		});

	// 	simulation.nodes(graph.nodes).on('tick', ticked);

	// 	simulation.force('link').links(graph.links);

	// 	function ticked() {
	// 		link
	// 			.attr('x1', function (d) {
	// 				return d.source.x;
	// 			})
	// 			.attr('y1', function (d) {
	// 				return d.source.y;
	// 			})
	// 			.attr('x2', function (d) {
	// 				return d.target.x;
	// 			})
	// 			.attr('y2', function (d) {
	// 				return d.target.y;
	// 			});

	// 		node
	// 			.attr('cx', function (d) {
	// 				return d.x + 5;
	// 			})
	// 			.attr('cy', function (d) {
	// 				return d.y - 3;
	// 			});

	// 		label
	// 			.attr('x', function (d) {
	// 				return d.x;
	// 			})
	// 			.attr('y', function (d) {
	// 				return d.y;
	// 			});
	// 	}

	// 	var tooltip = d3
	// 		.select('body')
	// 		.append('div')
	// 		.style('position', 'absolute')
	// 		.style('visibility', 'hidden')
	// 		.style('color', 'white')
	// 		.style('padding', '8px')
	// 		.style('background-color', '#626D71')
	// 		.style('border-radius', '6px')
	// 		.style('text-align', 'center')
	// 		.style('width', 'auto')
	// 		.text('');
	// }

	// var graphUrl = ''; //filepath or url for your data goes here

	// onMount(() => {
	// 	d3.json(graphUrl, function (error, data) {
	// 		blogGraph(data);
	// 	});
	// });
</script>

<div id="networkGraph" />
