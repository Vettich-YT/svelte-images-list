<script lang="ts">
	import img1 from './assets/1.jpg';
	import img2 from './assets/2.jpg';
	import img3 from './assets/3.jpg';
	import img4 from './assets/4.jpg';
	import img5 from './assets/5.jpg';
	import img6 from './assets/6.jpg';

	let images = [img1, img2, img3, img4, img5, img6];

	let moving = '';
	let movingX = 0;
	let movingY = 0;
	let prevX = 0;
	let prevY = 0;
	$: movingStyle = moving ? `position: absolute; left: ${movingX}px; top: ${movingY}px` : '';

	const startMove = (img: string) => {
		moving = img;
	}

	const stopMove = () => {
		moving = '';
		movingX = movingY = prevX = prevY = 0;
	}

	const move = (to: string) => {
		if (!moving || to == moving) return;

		const fromIdx = images.findIndex(v => v === moving);
		const from = images.splice(fromIdx, 1);
		
		let toIdx = images.findIndex(v => v === to);
		if (toIdx >= fromIdx) toIdx++;
		images = [...images.splice(0, toIdx), ...from, ...images];
	}

	const startMovePosition = (e: MouseEvent) => {
		prevX = e.clientX;
		prevY = e.clientY;
	}

	const movePosition = (e: MouseEvent) => {
		movingX += e.clientX - prevX;
		movingY += e.clientY - prevY;
		prevX = e.clientX;
		prevY = e.clientY;
	}

</script>

<svelte:window on:mousedown={startMovePosition} on:mouseup={stopMove} on:mousemove={movePosition} />

<div class="images">
	{#each images as img (img)}
		<div
			draggable={true}
			class="item"
			on:mousedown|preventDefault={() => startMove(img)}
			on:mousemove={() => move(img)}
			>
			<img src={img} alt=""
				style={moving === img ? movingStyle : ''}
				>
		</div>
	{/each}
</div>

<style>
	.images {
		position: relative;
		display: flex;
		flex-wrap: wrap;
		gap: 8px;
	}

	.item {
		width: 150px;
		height: 150px;
		border-radius: 4px;
		overflow: hidden;
		cursor: move;
	}

	.item img {
		width: 150px;
		height: 150px;
		object-fit: cover;
		object-position: center;
	}
</style>
