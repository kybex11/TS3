<template>
    <div>

    </div>
</template>

<script>
import * as THREE from 'three';

export default {
    data() {
        return {
            isDragging: false,
            previousMousePosition: {
                x: 0,
                y: 0
            },
        };
    },
    mounted() {
        const width = window.innerWidth, height = window.innerHeight;

        const camera = new THREE.PerspectiveCamera(70, width / height, 0.01, 10);
        camera.position.z = 1;

        const scene = new THREE.Scene();

        const geometry = new THREE.BoxGeometry(0.2, 0.2, 0.2);
        const material = new THREE.MeshNormalMaterial();

        const mesh = new THREE.Mesh(geometry, material);
        scene.add(mesh);

        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(width, height);
        document.body.appendChild(renderer.domElement);

        const onDocumentMouseDown = (event) => {
            this.isDragging = true;
            this.previousMousePosition.x = event.clientX;
            this.previousMousePosition.y = event.clientY;
        };

        const onDocumentMouseMove = (event) => {
            if (this.isDragging) {
                const deltaX = event.clientX - this.previousMousePosition.x;
                const deltaY = event.clientY - this.previousMousePosition.y;

                const rotationSpeed = 0.005;
                mesh.rotation.y += deltaX * rotationSpeed;
                mesh.rotation.x += deltaY * rotationSpeed;

                this.previousMousePosition.x = event.clientX;
                this.previousMousePosition.y = event.clientY;

                renderer.render(scene, camera);
            }
        };

        const onDocumentMouseUp = () => {
            this.isDragging = false;
        };

        document.addEventListener('mousedown', onDocumentMouseDown, false);
        document.addEventListener('mousemove', onDocumentMouseMove, false);
        document.addEventListener('mouseup', onDocumentMouseUp, false);

        const animate = () => {
            requestAnimationFrame(animate);
            if (!this.isDragging) {
                renderer.render(scene, camera);
            }
        };

        animate();
    }
}
</script>

<style>
body {
    overflow: hidden;
}
</style>