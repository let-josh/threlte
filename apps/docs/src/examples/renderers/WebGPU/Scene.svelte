<script lang="ts">
  import { T, useThrelte } from '@threlte/core'
  import { OrbitControls } from '@threlte/extras'
  import Stats from 'three/addons/libs/stats.module.js'
  import * as THREE from 'three/webgpu'

  const { scene, dom, invalidate } = useThrelte()

  scene.background = new THREE.Color(0xc1c1c1)

  const count = 200

  const geometries: THREE.BufferGeometry[] = [
    new THREE.ConeGeometry(1.0, 2.0, 3, 1),
    new THREE.BoxGeometry(2.0, 2.0, 2.0),
    new THREE.PlaneGeometry(2.0, 2, 1, 1),
    new THREE.CapsuleGeometry(),
    new THREE.CircleGeometry(1.0, 3),
    new THREE.CylinderGeometry(1.0, 1.0, 2.0, 3, 1),
    new THREE.DodecahedronGeometry(1.0, 0),
    new THREE.IcosahedronGeometry(1.0, 0),
    new THREE.OctahedronGeometry(1.0, 0),
    new THREE.PolyhedronGeometry([0, 0, 0], [0, 0, 0], 1, 0),
    new THREE.RingGeometry(1.0, 1.5, 3),
    new THREE.SphereGeometry(1.0, 3, 2),
    new THREE.TetrahedronGeometry(1.0, 0),
    new THREE.TorusGeometry(1.0, 0.5, 3, 3),
    new THREE.TorusKnotGeometry(1.0, 0.5, 20, 3, 1, 1)
  ]

  // make `count` instances of each
  const meshes = geometries.map(
    (geometry) => new THREE.InstancedMesh(geometry, new THREE.MeshToonMaterial(), count)
  )

  const group = new THREE.Group()
  group.static = true

  const position = new THREE.Vector3()
  const quaternion = new THREE.Quaternion()
  const scale = new THREE.Vector3()

  const width = 40
  function randomizeMatrix(matrix: THREE.Matrix4) {
    // random position from -width -> width
    position
      .random()
      .multiplyScalar(2 * width)
      .subScalar(width)

    quaternion.random()

    const factorScale = 1
    scale.random().multiplyScalar(0.5).addScalar(0.35).multiplyScalar(factorScale)

    return matrix.compose(position, quaternion, scale)
  }

  const color = new THREE.Color()
  const matrix = new THREE.Matrix4()
  for (const mesh of meshes) {
    group.add(mesh)
    mesh.instanceMatrix.setUsage(THREE.DynamicDrawUsage)
    for (let i = 0; i < mesh.count; i += 1) {
      mesh.setColorAt(i, color.setHex(Math.random() * 0xffffff))
      mesh.setMatrixAt(i, randomizeMatrix(matrix))
    }
  }

  // const child = new THREE.Mesh(geometries[i % geometries.length], material)
  // child.matrix.decompose(child.position, child.quaternion, child.scale)
  // child.userData.rotationSpeed = new THREE.Vector3().random().multiplyScalar(0.05)
  // group.add(child)
  // }

  const stats = new Stats()
  dom.appendChild(stats.dom)
</script>

<T is={group} />

<T.PerspectiveCamera
  position.z={50}
  makeDefault
>
  <OrbitControls
    autoRotate
    enableZoom={false}
    autoRotateSpeed={1}
    onchange={invalidate}
  />
</T.PerspectiveCamera>

<T.DirectionalLight intensity={2} />
<T.AmbientLight />
