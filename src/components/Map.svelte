<!-- https://github.com/VictorCazanave/svg-maps -->
<!-- This map is based on the work of MapSVG (https://mapsvg.com). -->

<script lang="ts">
	import panzoom, { type PanZoom } from 'panzoom';
	import { onDestroy, onMount } from 'svelte';
	import * as d3 from 'd3';

	import mapData from './data.json';
	import Paths from './Paths.svelte';

	let svg: SVGElement;
	let panzoomInstance: PanZoom;
	let imgEl: SVGImageElement[] = [];
	let provinces: Record<string, SVGCircleElement> = {};

	interface NodeDatum extends d3.SimulationNodeDatum {
		id: string;
		x: number;
		y: number;
		fx?: number;
		fy?: number;
		group: 'province' | 'brand';
	}

	interface LinkDatum extends d3.SimulationLinkDatum<NodeDatum> {
		source: string | NodeDatum;
		target: string | NodeDatum;
	}

	let simulation: d3.Simulation<NodeDatum, LinkDatum>;

	const entries = [
		{
			name: 'Suntree',
			image: '/images/suntree.jpg',
			province: 'bkk',
			x: -100,
			y: -100
		},
		{
			name: 'Kilo Spirits',
			image: '/images/kilo-spirits.jpg',
			province: 'kbi',
			x: -100,
			y: -100
		},
		{
			name: 'Gulve Fruit Wine',
			image: '/images/gulve-fruit-wine.jpg',
			province: 'cti',
			x: -100,
			y: -100
		},
		{
			name: 'The Spirit of Chaiyaphum',
			image: '/images/the-spirit-of-chaiyaphum.jpg',
			province: 'cpm',
			x: -100,
			y: -100
		},
		{
			name: 'Maa Jai Dum',
			image: '/images/maajaidum.jpg',
			province: 'cmi',
			x: -100,
			y: -100
		},
		{
			name: 'Koyote Lumka',
			image: '/images/koyote-lumka.jpg',
			province: 'cmi',
			x: -100,
			y: -100
		},
		{
			name: 'Asura',
			image: '/images/asura.jpg',
			province: 'cmi',
			x: -100,
			y: -100
		},
		{
			name: 'Lanna Thai Spirit',
			image: '/images/lanna-thai-spirit.jpg',
			province: 'cmi',
			x: -100,
			y: -100
		},
		{
			name: 'Saku',
			image: '/images/saku.jpg',
			province: 'nma',
			x: -100,
			y: -100
		},
		{
			name: 'Callmepapa',
			image: '/images/callmepapa.jpg',
			province: 'nbi',
			x: -100,
			y: -100
		}
	].map((entry) => {
		entry['x'] = mapData[entry.province as keyof typeof mapData].x;
		entry['y'] = mapData[entry.province as keyof typeof mapData].y;

		return entry;
	});

	let nodes: NodeDatum[] = [
		...entries.map((entry) => {
			return {
				id: entry.name,
				x: entry.x,
				y: entry.y,
				group: 'brand'
			} satisfies NodeDatum;
		}),
		...Object.entries(mapData)
			.filter(([provinceId, _]) => {
				// Filter only province that has a brand
				return entries.some((entry) => entry.province === provinceId);
			})
			.map(([provinceId, entry]) => {
				return {
					id: provinceId,
					x: entry.x,
					y: entry.y,
					fx: entry.x,
					fy: entry.y,
					group: 'province'
				} satisfies NodeDatum;
			})
	];

	let links: LinkDatum[] = [
		// Links every brand to its province
		...entries.map((entry) => {
			return {
				source: entry.province,
				target: entry.name
			} satisfies LinkDatum;
		})
	];

	function isNodeObject<T>(node: number | string | T): node is T {
		return typeof node !== 'number' && typeof node !== 'string';
	}

	function simulationUpdate() {
		// simulation.tick();
		nodes = [...nodes];
		links = [...links];
	}

	function getBrandImage(name: string) {
		const entry = entries.find((entry) => entry.name === name);

		return entry ? entry.image : '';
	}

	onMount(() => {
		simulation = d3.forceSimulation(nodes);

		simulation
			.force(
				'link',
				d3
					.forceLink(links)
					.id((d: any) => d.id)
					.distance((link) => {
						if (isNodeObject(link.source) && isNodeObject(link.target)) {
							// If source province has only 1 brand, return 0
							if (
								links.filter((l) => (l.source as NodeDatum).id === (link.source as NodeDatum).id)
									.length === 1
							) {
								return 0;
							}
						}

						return 30;
					})
			)
			.force('charge', d3.forceManyBody())
			// .force(
			// 	'collide',
			// 	d3.forceCollide(() => 10)
			// )
			.on('tick', simulationUpdate);

		panzoomInstance = panzoom(svg, {
			onClick: (e) => {
				e.preventDefault();

				// Handle only path elements

				const el = e.target as SVGPathElement;

				if (el.tagName !== 'path') return;

				// alert(`You clicked: ${el.getAttribute('name')}`);

				// const bounds = el.getBoundingClientRect();
				const bounds = el.getBBox();

				const midX = bounds.x + bounds.width / 2;
				const midY = bounds.y + bounds.height / 2;

				console.log(midX, midY);
			}
		});

		console.log(imgEl);
		let data: Record<string, { x: number; y: number }> = {};
		document.querySelectorAll<SVGPathElement>('#map path').forEach((path) => {
			// Get middle point of each path
			const bounds = path.getBBox();
			const midX = bounds.x + bounds.width / 2;
			const midY = bounds.y + bounds.height / 2;

			console.log(path.getAttribute('name'), midX, midY);

			// data[path.getAttribute('id')!] = { x: midX, y: midY, w: bounds.width, h: bounds.height };
		});

		console.log({ data });
	});

	onDestroy(() => {
		panzoomInstance?.dispose();
	});
</script>

<div class="w-full h-full overflow-hidden">
	<svg
		id="map"
		xmlns="http://www.w3.org/2000/svg"
		fill="lightgray"
		viewBox="0 0 560 1025"
		aria-label="Map of Thailand"
		class="w-full h-full"
	>
		<g bind:this={svg} class="h-full">
			<Paths />

			{#each links as link}
				<g stroke="#444" stroke-opacity="0.6">
					{#if isNodeObject(link.source) && isNodeObject(link.target)}
						<line
							x1={link.source.x}
							y1={link.source.y}
							x2={link.target.x}
							y2={link.target.y}
							stroke-dasharray="4"
							stroke-width="2"
						>
							<title>{link.source.id}</title>
						</line>
					{/if}
				</g>
			{/each}

			{#each nodes as node}
				{#if node.group == 'brand'}
					<image
						href={getBrandImage(node.id)}
						x={node.x - 20}
						y={node.y - 20}
						width="40"
						height="40"
					/>
				{/if}
				<!-- <circle class="node" r="30" cx={node.x} cy={node.y}>
					<title>{node.id}</title>
				</circle> -->
			{/each}
		</g>
	</svg>
</div>
