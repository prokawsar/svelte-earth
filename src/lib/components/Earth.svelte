<script lang="ts">
	import { onMount } from 'svelte';
	import * as THREE from 'three';

	let earthcanvas: HTMLCanvasElement;

	onMount(() => {
		// Set up scene, camera, and renderer
		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(
			75,
			window.innerWidth / window.innerHeight,
			0.1,
			1000
		);
		const renderer = new THREE.WebGLRenderer({ alpha: true, canvas: earthcanvas });
		renderer.setSize(window.innerWidth, window.innerHeight);

		// Create Earth geometry and texture
		const geometry = new THREE.SphereGeometry(8, 40, 40);
		const textureLoader = new THREE.TextureLoader();
		const earthTexture = textureLoader.load('/images/earthmap4k.jpg'); // Replace with the path to your texture
		const material = new THREE.MeshBasicMaterial({ map: earthTexture });
		const earth = new THREE.Mesh(geometry, material);
		scene.add(earth);

		// Set camera position
		camera.position.z = 15;

		// Animation function
		const animate = () => {
			requestAnimationFrame(animate);
			// Rotate the Earth
			// earth.rotation.y += 0.001;

			renderer.render(scene, camera);
		};

		animate();
	});
</script>

<canvas bind:this={earthcanvas} />
