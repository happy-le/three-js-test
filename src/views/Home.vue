<template>
  <div class="home">
    <canvas id="three"></canvas>
  </div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
export default {
  data() {
    return {
      scene: null,
      canvas: null,
      renderer: null,
      camera: null,
      controls: null,
      location: [
        {
          x: 4,
          y: 8,
          z: 2,
          mag: "1"
        },
        {
          x: 4,
          y: 8,
          z: -2,
          mag: "2"
        },
        {
          x: 4,
          y: 16,
          z: 0,
          mag: "3"
        },
        {
          x: 0,
          y: 20,
          z: 0,
          mag: "4"
        },
        {
          x: -4,
          y: 12,
          z: 0,
          mag: "5"
        },
        {
          x: -5,
          y: 12,
          z: 1,
          mag: "6"
        }
      ]
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.scene = new THREE.Scene();
      this.camera = new THREE.PerspectiveCamera(
        75,
        window.innerWidth / window.innerHeight,
        0.1,
        1000
      );

      this.canvas = document.querySelector("#three");
      this.renderer = new THREE.WebGLRenderer({
        canvas: this.canvas,
        antialias: true
      });
      this.renderer.setSize(window.innerWidth, window.innerHeight);

      this.camera.position.z = 35;
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      this.controls.enableDamping = true;
      // this.controls.maxPolarAngle = Math.PI / 2;
      // this.controls.minPolarAngle = Math.PI / 2;

      var axes = new THREE.AxisHelper(100);
      this.scene.add(axes);

      this.addCone1();
      this.addCube();
      this.addCone();
      this.addSphere();
      // this.addOther();
      this.animate();
    },

    animate() {
      this.controls.update();
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    },

    addOther() {
      var shape = new THREE.Shape();
      shape.moveTo(0, 0);
      shape.lineTo(4, 0);
      shape.lineTo(3, 2);
      shape.lineTo(1, 2);
      shape.lineTo(0, 0);

      var extrudeSettings = {
        steps: 2,
        depth: 4,
        bevelEnabled: true,
        bevelThickness: 0,
        bevelSize: 1,
        bevelOffset: 0,
        bevelSegments: 1
      };

      var geometry = new THREE.ExtrudeGeometry(shape, extrudeSettings);
      // var material = new THREE.MeshBasicMaterial({ color: 0x409eff });
      // var mesh = new THREE.Mesh(geometry, material);
      // this.scene.add(mesh);

      const edges = new THREE.EdgesGeometry(geometry);
      const line = new THREE.LineSegments(
        edges,
        new THREE.LineBasicMaterial({ color: 0xffffff })
      );
      this.scene.add(line);
    },

    addCone1() {
      var geometry = new THREE.ConeGeometry(6, 8, 4, 1, false, Math.PI / 4);
      var material = new THREE.MeshBasicMaterial({ color: 0x409eff });
      var cone = new THREE.Mesh(geometry, material);

      const edges = new THREE.EdgesGeometry(geometry);
      const line = new THREE.LineSegments(
        edges,
        new THREE.LineBasicMaterial({ color: 0xffffff })
      );

      this.scene.add(line);
      this.scene.add(cone);
      cone.position.set(0, 4, 0);
      line.position.set(0, 4, 0);
    },

    addCube() {
      var geometry = new THREE.BoxGeometry(5, 8, 5);
      var material = new THREE.MeshBasicMaterial({ color: 0x409eff });
      var cube = new THREE.Mesh(geometry, material);

      const edges = new THREE.EdgesGeometry(geometry);
      const line = new THREE.LineSegments(
        edges,
        new THREE.LineBasicMaterial({ color: 0xffffff })
      );
      this.scene.add(line);
      this.scene.add(cube);
      cube.position.set(0, 7.8, 0);
      line.position.set(0, 7.8, 0);
    },

    addCone() {
      var geometry = new THREE.ConeGeometry(3.4, 5, 4, 1, false, Math.PI / 4);
      var material = new THREE.MeshMatcapMaterial({ color: 0x409eff });
      var cone = new THREE.Mesh(geometry, material);

      const edges = new THREE.EdgesGeometry(geometry);
      const line = new THREE.LineSegments(
        edges,
        new THREE.LineBasicMaterial({ color: 0xffffff })
      );

      this.scene.add(line);
      this.scene.add(cone);
      cone.position.set(0, 14, 0);
      line.position.set(0, 14, 0);
    },

    addSphere() {
      var geometry = new THREE.SphereGeometry(0.2, 32, 32);
      var material = new THREE.MeshMatcapMaterial({ color: 0xff0000 });

      // var lineGeometry = new THREE.Geometry();

      this.location.forEach(element => {
        var sphere = new THREE.Mesh(geometry, material);
        this.scene.add(sphere);
        sphere.position.set(element.x, element.y, element.z);

        // lineGeometry.vertices.push(
        //   new THREE.Vector3(element.x, element.y, element.z)
        // );
      });

      // var lineMaterial = new THREE.LineBasicMaterial({ color: 0x0000ff });
      // var line = new THREE.Line(lineGeometry, lineMaterial);
      // this.scene.add(line);
      // this.addLine();
    },

    addLine() {
      var material = new THREE.LineBasicMaterial({
        color: 0x0000ff
      });

      var geometry = new THREE.Geometry();
      geometry.vertices.push(
        new THREE.Vector3(-10, 0, 0),
        new THREE.Vector3(0, 10, 0),
        new THREE.Vector3(10, 0, 0)
      );

      var line = new THREE.Line(geometry, material);
      this.scene.add(line);
    }
  }
};
</script>

<style lang="scss" scoped>
#three {
  width: 100%;
  height: 100%;
  position: fixed;
  left: 0;
  top: 0;
}
</style>
