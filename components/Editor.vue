<template>
    <div class="firewall_container" ref="background">
        <div class="overlay" v-if="isSessionToggleOpen"></div>
        <div class="navbar-container">
            <div class="navbar-container-elements">
                <h3>TS3 Editor</h3>
                <div class="navbar-container-elements-right">
                    <button @click="toggleSessionsButton">Session</button>
                </div>
                <div class="navbar-container-elements-right">
                    <button>Edit</button>
                </div>
            </div>
        </div>
        <div class="container">
            <div class="elements-container">
            </div>
            <div class="preview-container">
                <div class="preview-module-view" ref="preview_module_view" tabindex="0"></div>
            </div>
        </div>
        <div class="popup-sessions-menu" v-if="isSessionToggleOpen">
            <button @click="toggleSessionsButton" class="noselect"><span class="text">Close</span><span class="icon"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24"><path d="M24 20.188l-8.315-8.209 8.2-8.282-3.697-3.697-8.212 8.318-8.31-8.203-3.666 3.666 8.321 8.24-8.206 8.313 3.666 3.666 8.237-8.318 8.285 8.203z"></path></svg></span></button>
        </div>
    </div>
</template>
<style>
.popup-sessions-menu button {
    width: 150px;
    height: 50px;
    cursor: pointer;
    display: flex;
    align-items: center;
    background: red;
    border: none;
    border-radius: 5px;
    box-shadow: 1px 1px 3px rgba(0,0,0,0.15);
    background: #e62222;
   }
   
   .popup-sessions-menu button, .popup-sessions-menu button span {
    transition: 200ms;
   }
   
   .popup-sessions-menu button .text {
    transform: translateX(35px);
    color: white;
    font-weight: bold;
   }
   
   .popup-sessions-menu button .icon {
    position: absolute;
    border-left: 1px solid #c41b1b;
    transform: translateX(110px);
    height: 40px;
    width: 40px;
    display: flex;
    align-items: center;
    justify-content: center;
   }
   
   .popup-sessions-menu button svg {
    width: 15px;
    fill: #eee;
   }
   
   .popup-sessions-menu button:hover {
    background: #ff3636;
   }
   
   .popup-sessions-menu button:hover .text {
    color: transparent;
   }
   
   .popup-sessions-menu button:hover .icon {
    width: 150px;
    border-left: none;
    transform: translateX(0);
   }
   
   .popup-sessions-menu button:focus {
    outline: none;
   }
   
   .popup-sessions-menu button:active .icon svg {
    transform: scale(0.8);
   }
.overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0,0,0,0.5);
    z-index: 99;
}

.blur > :not(.popup-sessions-menu) {
    filter: blur(5px);
}
.popup-sessions-menu {
    position: fixed;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    z-index: 100;
    background-color: white;
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}
.navbar-container-elements-right {
    padding-left: 20px;
}
.navbar-container-elements {
    display: flex;
}
.navbar-container-elements button {
    border: none;
    background: none;
    color: white;
    font-family:'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
    cursor: pointer;
    font-size: 1.3rem;
    display: flex;
    justify-content: center;
    text-align: center;
    align-items: center;
    padding-top: 15px;
}

.navbar-container {
    -webkit-user-select: none;
    user-select: none;
    top: 0;
    left: 0;
    position: fixed;
    color: white;
    font-family:'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
    width: 100%;
    height: 50px;
    padding-left: 10px;
    background-color: black;
}
.container {
    display: flex;
    height: 100%;
    padding-top: 50px;
}
.elements-container {
    text-align: left;
    color: white;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.preview-container {
    flex: 3;
    align-items: center;
    justify-content: center;
    display: flex;
}
.preview-module-view {
    background-color: black;
    display: flex;
    justify-content: center;
    text-align: center;
    align-items: center;
    border: none;
}
</style>

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
            isSessionToggleOpen: false,
        };
    },
    methods: {
        toggleSessionsButton() {
            this.isSessionToggleOpen = !this.isSessionToggleOpen;

            if (this.isSessionToggleOpen) {
                this.$refs.background.classList.add('blur');
            } else {
                this.$refs.background.classList.remove('blur');
            }
        }
    },
    mounted() {
        let view = this.$refs.preview_module_view;
        let width = 900, height = 600;

        const camera = new THREE.PerspectiveCamera(70, width / height, 0.01, 10);
        camera.position.z = 1;

        const scene = new THREE.Scene();

        const background_loader = new THREE.TextureLoader();
        background_loader.load('/scene_background.jpg', function(tex) {
            scene.background = tex;
        })

        const geometry = new THREE.BoxGeometry(0.2, 0.2, 0.2);
        const material = new THREE.MeshNormalMaterial();

        const mesh = new THREE.Mesh(geometry, material);
        scene.add(mesh);

        const renderer = new THREE.WebGLRenderer({ antialias: true });
        renderer.setSize(width, height);
        if (view) {
            view.appendChild(renderer.domElement);
        }

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
                camera.rotation.y += deltaX * rotationSpeed;
                camera.rotation.x += deltaY * rotationSpeed;

                this.previousMousePosition.x = event.clientX;
                this.previousMousePosition.y = event.clientY;

                renderer.render(scene, camera);
            }
        };

        const onDocumentMouseUp = () => {
            this.isDragging = false;
        };

        const onDocumentKeyDown = (e) => {
            if (e.key === "W" || e.key === "w" || e.key === "ц" || e.key === "Ц") {
                camera.position.z -= 0.1;
            }
            if (e.key === "S" || e.key === "s" || e.key === "ы" || e.key === "ы") {
                camera.position.z += 0.1;
            }
            if (e.key === "A" || e.key === "a" || e.key === "ф" || e.key === "ф") {
                camera.position.x -= 0.1;
            }
            if (e.key === "D" || e.key === "d" || e.key === "в" || e.key === "в") {
                camera.position.x += 0.1;
            }
            if (e.key === " ") {
                camera.position.y += 0.1;
            }
            if (e.key === "Shift") {
                camera.position.y -= 0.1;
            }
        }

        if (view instanceof HTMLElement) {
            view.addEventListener('keydown', onDocumentKeyDown, false);

            view.addEventListener('mousedown', onDocumentMouseDown, false);
            view.addEventListener('mousemove', onDocumentMouseMove, false);
            view.addEventListener('mouseup', onDocumentMouseUp, false);
        }

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