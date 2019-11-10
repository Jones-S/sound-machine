<script>
  import { onMount } from 'svelte'
  import { Renderer, Transform, Camera, Box, Program, Mesh } from 'ogl'

  let scene = null
  let geometry = null
  let program = null
  let mesh = null
  let gl = null
  let camera = null
  let renderer = null

	function initWebGL() {
    renderer = new Renderer()
    gl = renderer.gl
    document.body.appendChild(gl.canvas)

    camera = new Camera(gl)
    camera.position.z = 5

    window.addEventListener('resize', resize, false)
    resize()

    scene = new Transform()

    geometry = new Box(gl)

    program = new Program(gl, {
      vertex: `
        attribute vec3 position;

        uniform mat4 modelViewMatrix;
        uniform mat4 projectionMatrix;

        void main() {
            gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
        }
        `,
      fragment: `
        void main() {
            gl_FragColor = vec4(1.0);
        }
      `,
    })

    mesh = new Mesh(gl, {geometry, program})
    mesh.setParent(scene)

    requestAnimationFrame(update)
  }

  function resize() {
    renderer.setSize(window.innerWidth, window.innerHeight)
    camera.perspective({
      aspect: gl.canvas.width / gl.canvas.height,
    })
  }

  function update(t) {
    requestAnimationFrame(update)

    mesh.rotation.y -= 0.04
    mesh.rotation.x += 0.03
    renderer.render({scene, camera})
  }

	onMount(() => {
		initWebGL()
  })

</script>

<!-- eslint-disable -->
<style lang="sass">
	@import './PulsingSphere.scss';
</style>
<!-- eslint-enable -->

<h1>Hello Sphere!</h1>

