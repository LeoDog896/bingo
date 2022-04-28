<script lang="ts">
	import * as THREE from 'three';
	import * as SC from 'svelte-cubed';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
    import { onMount } from 'svelte/internal';
    
	let scene: THREE.Group;

    onMount(() => {
        const loader = new GLTFLoader();

        loader.load( 'roller.glb', function ( gltf ) {

			scene = gltf.scene

		}, undefined, function ( error ) {
			console.error( error );

		});
    })
</script>

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

	<SC.Primitive
		object={scene}
		position={[0, new THREE.Box3().setFromObject(scene).getSize(new THREE.Vector3()).y - 2, 0]}
		rotation={[0, 0, 0]}
	/>

	<SC.PerspectiveCamera position={[1, 1, 3]} zoom={0.5} />
	<SC.OrbitControls enableZoom={false} maxPolarAngle={Math.PI * 0.51} />
	<SC.AmbientLight intensity={0.6} />
	<SC.DirectionalLight intensity={0.6} position={[-2, 3, 2]} shadow={{ mapSize: [2048, 2048] }} />
</SC.Canvas>