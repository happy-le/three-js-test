<template>
  <div class="home">
    <canvas id="three"></canvas>
  </div>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
import { GLTFLoader } from "three/examples/jsm/loaders/GLTFLoader";

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
          x: 70,
          y: 80,
          z: 10,
          msg: "1"
        },
        {
          x: 70,
          y: 80,
          z: -10,
          msg: "2"
        },
        {
          x: 50,
          y: 130,
          z: 0,
          msg: "3"
        },
        {
          x: 0,
          y: 220,
          z: 0,
          msg: "4"
        },
        {
          x: -45,
          y: 130,
          z: 5,
          msg: "5"
        },
        {
          x: -60,
          y: 80,
          z: 5,
          msg: "6"
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

      var axes = new THREE.AxisHelper(500);
      this.scene.add(axes);

      this.addModel();
      // this.addCone1();
      // this.addCube();
      // this.addCone();
      this.addSphere();
      // this.addLine();
      this.addText();

      this.animate();
    },

    animate() {
      this.controls.update();
      this.renderer.render(this.scene, this.camera);
      requestAnimationFrame(this.animate);
    },

    /**
     * 塔 - 模型
     */
    addModel() {
      let self = this;
      var loader = new GLTFLoader();

      loader.load(
        "static/electric/scene.gltf",
        function(gltf) {
          // 解决模型为黑色的原因
          gltf.scene.traverse(function(child) {
            if (child.isMesh) {
              child.material.emissive = child.material.color;
              child.material.emissiveMap = child.material.map;
            }
          });

          var mesh = gltf.scene;
          // let material = new THREE.MeshBasicMaterial({
          //   color: 0x409eff
          // });
          // var mesh1 = new THREE.Mesh(gltf, material);
          mesh.scale.set(0.5, 0.5, 0.5);
          mesh.translateX(-12);
          mesh.rotateY(Math.PI / 2);
          self.scene.add(mesh); // 将模型引入three
          console.log("加载完成");
        },
        // called while loading is progressing
        function(xhr) {
          console.log((xhr.loaded / xhr.total) * 100 + "% loaded");
        },
        // called when loading has errors
        function(error) {
          console.error("An error happened", error);
        }
      );
    },

    /**
     * 下 - 四棱锥
     */
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

    /**
     * 中 - 长方体
     */
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

    /**
     * 上四棱锥
     */
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

    /**
     * 航点
     */
    addSphere() {
      var geometry = new THREE.SphereGeometry(2, 32, 32);
      var material = new THREE.MeshMatcapMaterial({ color: 0xff0000 });

      this.location.forEach(element => {
        var sphere = new THREE.Mesh(geometry, material);
        this.scene.add(sphere);
        sphere.position.set(element.x, element.y, element.z);
      });
    },

    /**
     * 航点连接线
     */
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
    },

    createCanvas(text) {
      var canvas = document.createElement("canvas");
      var width = 100;
      var height = 100;
      canvas.width = width;
      canvas.height = height;
      var context = canvas.getContext("2d");

      context.fillStyle = "rgba(0, 0, 0, 0)";
      context.fillRect(0, 0, width, height);

      /* 字体颜色 */
      context.fillStyle = "rgba(255, 255, 0, 1)";
      context.font = "100px bolder";
      /**文字 */
      console.log("text:", text);
      context.fillText(text, 0, 90);
      return canvas;
    },

    addText() {
      this.location.forEach(element => {
        var texture = new THREE.Texture(this.createCanvas(element.msg)); //就是上面的canvas，我将它写在一个函数中然后返回。
        texture.needsUpdate = true;

        var spriteMaterial = new THREE.SpriteMaterial({ map: texture });
        var sprite = new THREE.Sprite(spriteMaterial);
        sprite.scale.set(5, 5, 5); //大小缩放
        sprite.position.set(element.x + 5, element.y + 5, element.z + 5); //位置
        this.scene.add(sprite); //将其放入场景中
      });
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
