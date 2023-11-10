<script lang="ts">
	import { onDestroy, onMount } from 'svelte';
	import * as THREE from 'three';
	import { PointerLockControls } from 'three/examples/jsm/controls/PointerLockControls';

	let earthcanvas: HTMLCanvasElement;
	let previousMouseX = 0;
	let previousMouseY = 0;
	let accumulatedRotationX = 0;
	let accumulatedRotationY = 0;

	let cameraZoom = 15; // Initial camera zoom

	onMount(() => {
		// Set up scene, camera, and renderer
		const scene = new THREE.Scene();
		const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 1, 100);
		const renderer = new THREE.WebGLRenderer({ alpha: true, canvas: earthcanvas });
		renderer.setSize(window.innerWidth, window.innerHeight);

		// Create Earth geometry and texture
		const geometry = new THREE.SphereGeometry(8, 80, 80);
		const textureLoader = new THREE.TextureLoader();
		const earthTexture = textureLoader.load('/images/earthmap4k.jpg'); // Replace with the path to your texture
		const material = new THREE.MeshBasicMaterial({ map: earthTexture });
		const earth = new THREE.Mesh(geometry, material);
		scene.add(earth);

		// Set camera position
		camera.position.z = cameraZoom;

		let isMousePressed = false;

		// Handle mouse click to enable controls
		document.addEventListener('mousedown', (event) => {
			isMousePressed = true;
		});

		document.addEventListener('mouseup', () => {
			isMousePressed = false;

			accumulatedRotationX = earth.rotation.x;
			accumulatedRotationY = earth.rotation.y;
		});

		// Handle mouse movement
		document.addEventListener('mousemove', (event) => {
			if (isMousePressed) {
				const deltaX = event.clientX - previousMouseX;
				const deltaY = event.clientY - previousMouseY;

				accumulatedRotationY += deltaX * 0.005;
				accumulatedRotationX += deltaY * 0.005;

				earth.rotation.y = accumulatedRotationY;
				earth.rotation.x = accumulatedRotationX;

				previousMouseX = event.clientX;
				previousMouseY = event.clientY;
			}
		});

		// Handle mouse wheel for zoom
		document.addEventListener('wheel', (event) => {
			// Adjust the zoom speed as needed
			const zoomSpeed = 0.1;

			// Update the camera zoom based on the wheel delta
			cameraZoom -= event.deltaY * zoomSpeed;

			// Limit the zoom range if necessary
			cameraZoom = Math.max(cameraZoom, 5); // Minimum zoom
			cameraZoom = Math.min(cameraZoom, 30); // Maximum zoom

			camera.position.z = cameraZoom;
		});

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
