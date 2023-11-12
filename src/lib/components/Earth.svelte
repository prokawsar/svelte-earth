<script lang="ts">
	import { sceneStore } from '$lib/store'
	import { onDestroy, onMount } from 'svelte'
	import * as THREE from 'three'
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls'
	import ThreeGlobe from 'three-globe'

	let earthcanvas: HTMLCanvasElement
	let cameraZoom = 15 // Initial camera zoom
	let camera: THREE.PerspectiveCamera, renderer: THREE.Renderer, scene: THREE.Scene
	let _globe

	const initVariables = () => {
		// Set up scene, camera, and renderer
		scene = new THREE.Scene()
		camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 1, 100)
		renderer = new THREE.WebGLRenderer({ alpha: false, canvas: earthcanvas })
		renderer.setSize(window.innerWidth, window.innerHeight)
	}

	const onWindowResize = () => {
		camera.aspect = window.innerWidth / window.innerHeight
		camera.updateProjectionMatrix()
		renderer.setSize(window.innerWidth, window.innerHeight)
	}

	const setControls = () => {
		const controls = new OrbitControls(camera, renderer.domElement)
		controls.rotateSpeed = 0.4
		controls.update()
	}

	onMount(() => {
		initVariables()
		setEarthImage()
		setControls()
		setCamera()
		animate()
		initGlobe()

		window.addEventListener('resize', onWindowResize, false)
		sceneStore.set(scene)
	})

	const initGlobe = () => {
		_globe = new ThreeGlobe({
			animateIn: true
		})

		// scene.add(_globe)
	}

	const setEarthImage = () => {
		// Create Earth geometry and texture
		const geometry = new THREE.SphereGeometry(8, 80, 80)
		const textureLoader = new THREE.TextureLoader()
		const earthTexture = textureLoader.load('/images/earthmap4k.jpg') // Replace with the path to your texture
		const material = new THREE.MeshBasicMaterial({ map: earthTexture })
		const earth = new THREE.Mesh(geometry, material)
		scene.add(earth)
	}

	const setCamera = () => {
		// Set camera position
		camera.position.z = cameraZoom
		camera.position.x = 0
		camera.position.y = 0
	}

	// Animation function
	const animate = () => {
		requestAnimationFrame(animate)
		renderer.render(scene, camera)
	}
</script>

<canvas bind:this={earthcanvas} />
