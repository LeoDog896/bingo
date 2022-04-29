<script lang="ts">
	import * as CANNON from "cannon-es";
	import * as PE from "svelte-cannon";
	import * as THREE from "three";
	import * as SC from "svelte-cubed";
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
    import { onMount } from 'svelte/internal';
    
	let rotator: THREE.Group;
	let stand: THREE.Group;

	let spin = 0;

	SC.onFrame(() => {
		spin += 0.01;
	});

	let position = new CANNON.Vec3(0, 0, 0);

    onMount(() => {
        const loader = new GLTFLoader();

        loader.load('rotator.glb', function (gltf) {
			rotator = gltf.scene
		}, undefined, console.error);

		loader.load( 'stand.glb', function (gltf) {
			stand = gltf.scene
		}, undefined, console.error);
    })
</script>

<PE.World gravity={[0, -9.81, 0]}>
	<PE.Body mass={1} bind:position>
	  <PE.Sphere radius={1} />
	  <PE.Primitive></PE.Primitive>
	</PE.Body>
  </PE.World>

<SC.Canvas
	antialias
	background={new THREE.Color('papayawhip')}
	fog={new THREE.FogExp2('papayawhip', 0.1)}
	shadows
>
	<SC.Group position={[0, -1, 0]}>
		<SC.Mesh
			geometry={new THREE.PlaneGeometry(50, 50)}
			material={new THREE.MeshStandardMaterial({ color: 'burlywood' })}
			rotation={[-Math.PI / 2, 0, 0]}
			receiveShadow
		/>
		<SC.Primitive
			object={new THREE.GridHelper(50, 50, 'papayawhip', 'papayawhip')}
			position={[0, 0.001, 0]}
		/>
	</SC.Group>

	{#if rotator}
		<SC.Primitive
			object={rotator}
			position={[0, new THREE.Box3().setFromObject(rotator).getSize(new THREE.Vector3()).y - 2, 0]}
			rotation={[spin, 0, 0]}
		/>
	{/if}

	{#if stand}
		<SC.Primitive
			object={stand}
			position={[0, new THREE.Box3().setFromObject(rotator).getSize(new THREE.Vector3()).y - 2, 0]}
			rotation={[0, 0, 0]}
		/>
	{/if}

	<SC.Mesh geometry={new THREE.SphereGeometry()} position={[position.x, position.y, position.z]} scale={[0.25, 0.25, 0.25]} />

	<SC.PerspectiveCamera position={[1, 1, 3]} zoom={0.5} />
	<SC.OrbitControls enableZoom={false} maxPolarAngle={Math.PI * 0.51} />
	<SC.AmbientLight intensity={0.6} />
	<SC.DirectionalLight intensity={0.6} position={[-2, 3, 2]} shadow={{ mapSize: [2048, 2048] }} />
</SC.Canvas>