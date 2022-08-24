<template>
	<div id="product-configurator" @dragover.prevent @drop.prevent>
		<header>
			<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
				<div class="container-fluid">
					<a class="navbar-brand" href="#">Product Configurator</a>
					<button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNavAltMarkup" aria-controls="navbarNavAltMarkup" aria-expanded="false" aria-label="Toggle navigation">
						<span class="navbar-toggler-icon"></span>
					</button>
					<div class="collapse navbar-collapse" id="navbarNavAltMarkup">
						<div class="navbar-nav">
							<a class="nav-link" aria-current="page" href="#">Home</a>
						</div>
					</div>
				</div>
			</nav>
		</header>

		<main>	
			<div class="container-fluid">

				<div class="row">

					<div class="col-md-2">
						<ModelList :models="models"/>
					</div>

					<div class="col-md-8">
						<canvas @drop="dragFile" id="renderCanvas" class="16by9 w-100"></canvas>
					</div>

					<div id="selected-object-details" class="col-md-2">
						<div v-if="modelSelected === true">
							<h1> {{ selectedModel.name }} </h1>

							<!-- inline input group for position -->
							<div class="row my-2">
								<label>Position</label>
								
								<div class="col">
									<label for="positionX">X</label>
									<input type="number" class="form-control" id="positionX" v-model="selectedModel.position.x" />
								</div>

								<div class="col">
									<label for="positionY">Y</label>
									<input type="number" class="form-control" id="positionY" v-model="selectedModel.position.y" />
								</div>

								<div class="col">
									<label for="positionZ">Z</label>
									<input type="number" class="form-control" id="positionZ" v-model="selectedModel.position.z" />
								</div>
							</div>

							<!-- input group for rotation -->
							<div class="row my-2">
								<label>Rotation</label>
								
								<div class="col">
									<label for="rotationX">X</label>
									<input type="number" class="form-control" id="rotationX" v-model="selectedModel.rotationQuaternion.x" />
								</div>

								<div class="col">
									<label for="rotationY">Y</label>
									<input type="number" class="form-control" id="rotationY" v-model="selectedModel.rotationQuaternion.y" />
								</div>

								<div class="col">
									<label for="rotationZ">Z</label>
									<input type="number" class="form-control" id="rotationZ" v-model="selectedModel.rotationQuaternion.z" />
								</div>
							</div>
							
							<!-- input group for scale -->
							<div class="row my-2">
								<label>Scale</label>
								
								<div class="col">
									<label for="scaleX">X</label>
									<input type="number" class="form-control" id="scaleX" v-model="selectedModel.scaling.x" />
								</div>

								<div class="col">
									<label for="scaleY">Y</label>
									<input type="number" class="form-control" id="scaleY" v-model="selectedModel.scaling.y" />
								</div>

								<div class="col">
									<label for="scaleZ">Z</label>
									<input type="number" class="form-control" id="scaleZ" v-model="selectedModel.scaling.z" />
								</div>
							</div>

						</div>

						<div v-else>
							<p>No model selected</p>	
						</div>

					</div> <!-- /#selected-object-details  -->

				</div>
			</div>
		</main> <!-- /main -->
	</div>

</template>

<script>

export default {
	name: 'App',
	components: {
		ModelList: () => import('./components/ModelList.vue'),
	},
	data() {
		return {
			selectedModel: {},
			modelSelected: false,
			models: [],
			canvas: undefined,
			engine: undefined,
			scene: undefined,
			camera: undefined,
			light: undefined,
			ground: undefined,
			groundMaterial: undefined,
			File: []
		};
	},
	/*computed: {
		selectedModelPosition() {
			if (this.modelSelected === true) {

				return { x: this.selectedModel.position._x.toFixed(2), y: this.selectedModel.position._y.toFixed(2), z: this.selectedModel.position._z.toFixed(2) };
			}
			return { x: 0, y: 0, z: 0 };
		},
		selectedModelRotation() {
			if (this.modelSelected === true) {
				console.log(this.selectedModel.rotationQuaternion)
				return { x: this.selectedModel.rotationQuaternion.x.toFixed(2), y: this.selectedModel.rotationQuaternion.y.toFixed(2), z: this.selectedModel.rotationQuaternion.z.toFixed(2) };
			}
			return { x: 0, y: 0, z: 0 };
		},
		selectedModelScale() {
			if (this.modelSelected === true) {
				return { x: this.selectedModel.scale._x.toFixed(2), y: this.selectedModel.scale._y.toFixed(2), z: this.selectedModel.scale._z.toFixed(2) };
			}
			return { x: 1, y: 1, z: 1 };
		}
	},*/
	methods: {
		dragFile(e) {
			this.File = e.dataTransfer.files;
			this.parseUploadedFile(this.File[0]);
		},


		parseUploadedFile(file) {
			var _self = this;
			var reader = new FileReader();

			reader.addEventListener("load", function (e) {

				const datastring = e.target.result;
				var model_data = btoa([].reduce.call(new Uint8Array(datastring), function (p, c) { return p + String.fromCharCode(c) }, ''))
				var base64_model_content = "data:;base64," + model_data;
				var raw_content = BABYLON.FileTools.DecodeBase64UrlToBinary(base64_model_content);

				var blob = new Blob([raw_content]);
				var url = URL.createObjectURL(blob);

				BABYLON.SceneLoader.Append("", url, _self.scene, function (baby) {

					let model = baby.meshes[_self.models.length + 1];

					model.material = new BABYLON.StandardMaterial("model-material", _self.scene);
					model.material.alpha = 1;
					model.material.backFaceCulling = false;
					model.name = file.name;
					model.isPickable = true;

					_self.gizmoManager.attachToMesh(model);

					_self.models.push(model);

					_self.modelSelected = true;
					_self.selectedModel = model;
				
				}, undefined, undefined, ".stl");
			})

			reader.readAsArrayBuffer(file);
		},


		createCamera() {
			this.camera = new BABYLON.ArcRotateCamera("Camera", 0, 0, 0, new BABYLON.Vector3(0, 0, 0), this.scene);
			this.camera.setPosition(new BABYLON.Vector3(70, 150, 200));
			this.camera.attachControl(this.canvas, true);
		},


		loadAssets() {
			this.createCamera();
			this.createLight();
			this.createGround();
		},


		createLight() {
			this.light = new BABYLON.HemisphericLight('light1', new BABYLON.Vector3(0, 1, 0), this.scene);
			this.light.intensity = 0.7;
			this.light.castShadows = true;
		},


		createGround() {
			this.groundMaterial = new BABYLON.GridMaterial("groundMaterial", this.scene);
			this.groundMaterial.opacity = 0.9999;
			this.groundMaterial.backFaceCulling = false;

			this.ground = BABYLON.MeshBuilder.CreateGround('ground', { width: 200, height: 200 }, this.scene);
			this.ground.material = this.groundMaterial;
			this.ground.receiveShadows = true;
			this.ground.isPickable = false;
		},


		enableGizmos() {

			this.gizmoManager = new BABYLON.GizmoManager(this.scene);

			this.gizmoManager.positionGizmoEnabled = true;
			this.gizmoManager.rotationGizmoEnabled = true;
			this.gizmoManager.boundingBoxGizmoEnabled = true;

			this.gizmoManager.clearGizmoOnEmptyPointerEvent = true;
			this.gizmoManager.usePointerToAttachGizmos = true;

			this.gizmoManager.gizmos.positionGizmo.snapDistance = 0.1;
			this.gizmoManager.gizmos.boundingBoxGizmo.scaleBoxSize = 1;
			this.gizmoManager.gizmos.boundingBoxGizmo.rotationSphereSize = 0;

		},


	},
	mounted() {

		var _self = this;

		// initialize babylon engine
		this.canvas = document.getElementById('renderCanvas');
		this.engine = new BABYLON.Engine(this.canvas, true);

		// setup scene
		this.scene = new BABYLON.Scene(this.engine);
		this.scene.clearColor = new BABYLON.Color3(0.1, 0.1, 0.13);

		this.enableGizmos();

		this.gizmoManager.onAttachedToMeshObservable.add((gizmo) => {
			if (gizmo) {
				
				_self.selectedModel = gizmo
				_self.modelSelected = true;

			}
			else {
				_self.selectedModel = {};
				_self.modelSelected = false;
			}
		});

		this.loadAssets();

		

		this.engine.runRenderLoop(() => {
			this.scene.render();
		});

	}
}
</script>

<style lang="scss">
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
