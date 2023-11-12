<script lang="ts">
	import { sceneStore } from '$lib/store';
	import { onDestroy, onMount } from 'svelte';
	import * as THREE from 'three';
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

	let earthcanvas: HTMLCanvasElement;
	let cameraZoom = 15; // Initial camera zoom
	let camera: THREE.PerspectiveCamera, renderer: THREE.Renderer, scene: THREE.Scene;

	const onWindowResize = () => {
		camera.aspect = window.innerWidth / window.innerHeight;
		camera.updateProjectionMatrix();
		renderer.setSize(window.innerWidth, window.innerHeight);
	};

	onMount(() => {
		// Set up scene, camera, and renderer
		scene = new THREE.Scene();
		camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 1, 100);
		renderer = new THREE.WebGLRenderer({ alpha: false, canvas: earthcanvas });
		renderer.setSize(window.innerWidth, window.innerHeight);

		// Create Earth geometry and texture
		const geometry = new THREE.SphereGeometry(8, 80, 80);
		const textureLoader = new THREE.TextureLoader();
		const earthTexture = textureLoader.load('/images/earthmap4k.jpg'); // Replace with the path to your texture
		const material = new THREE.MeshBasicMaterial({ map: earthTexture });
		const earth = new THREE.Mesh(geometry, material);
		scene.add(earth);

		const controls = new OrbitControls(camera, renderer.domElement);
		controls.rotateSpeed = 0.4;
		controls.update();

		// Set camera position
		camera.position.z = cameraZoom;
		camera.position.x = 0;
		camera.position.y = 0;

		window.addEventListener('resize', onWindowResize, false);

		// Animation function
		const animate = () => {
			requestAnimationFrame(animate);
			// Rotate the Earth
			// earth.rotation.y += 0.001;

			renderer.render(scene, camera);
		};

		animate();
		sceneStore.set(scene);
	});
</script>

<canvas bind:this={earthcanvas} />
