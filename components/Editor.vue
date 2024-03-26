<template>
    <div class="firewall_container" ref="background">
        <div class="overlay" @click="closeToggleMenus" v-if="isSessionToggleOpen || isEditToggleOpen || isColorToggleOpen"></div>
        <div class="navbar-container">
            <div class="navbar-container-elements">
                <h3>TS3 Editor</h3>
                <div class="navbar-container-elements-right">
                    <button @click="toggleSessionsButton">Session</button>
                </div>
                <div class="navbar-container-elements-right">
                    <button @click="toggleEditButton">Edit</button>
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
            <button @click="toggleSessionsButton">Close <-</button>
        </div>
        <div class="popup-sessions-menu" v-if="isEditToggleOpen">
            <button @click="toggleEditButton">Close <-</button>
            <br>
            <button @click="toggleColorOpenAndEditClose">Color -></button>
        </div>
        
        <div class="popup-sessions-menu" v-if="isColorToggleOpen">
            <button @click="toggleColorOpen">Close <-</button>
            <br>
            <input type="text" placeholder="Enter cube color" ref="cubeColor" @change="cubeColorChangeEvent">
            <br>
            <input type="text" placeholder="Enter outline color" ref="cubeOutline" @change="cubeOutlineChangeEvent">
        </div>
    </div>
</template>
<style>
.popup-sessions-menu input {
    border: none;
    color: black;
    font-size: 1rem;
}

.popup-sessions-menu button {
    user-select: none;
    border: none;
    background: none;
    font-size: 1.3rem;
    cursor: pointer;
    padding: 10px;
    font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
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
            material: null,
            isDragging: false,
            previousMousePosition: {
                x: 0,
                y: 0
            },
            isSessionToggleOpen: false,
            isEditToggleOpen: false,
            isColorToggleOpen: false,
        };
    },
    methods: {
        handleMouseMove(e) {
            const view = this.$refs.preview_module_view;
            if (view) {
                const rect = view.getBoundingClientRect();
                const mouseX = e.clientX - rect.left;
                const mouseY = e.clientY - rect.top;

                if (mouseX < 0 || mouseY < 0 || mouseX > rect.width || mouseY >rect.height) {
                    const centerX = rect.width / 2;
                    const centerY = rect.height / 2;
                    window.scrollTo(rect.left + centerX, rect.top + centerY);
                }
            }
        },
        hideCursorAndFocus() {
            const view = this.$refs.preview_module_view;
            if (view) {
                view.style.cursor = 'none';
                view.setAttribute('tabindex', '-1');
                view.focus();
            }
        },
        handleDocumentClick() {
            const view = this.$refs.preview_module_view;
            if (view && !view.contains(event.target)) {
                view.style.cursor = '';
                view.removeAttribute('tabindex');
            }
        },
        closeToggleMenus() {
            if (this.isSessionToggleOpen) {
                this.toggleSessionsButton();
            } else if (this.isEditToggleOpen) {
                this.toggleEditButton();
            } else if (this.isColorToggleOpen) {
                this.toggleColorOpen();
            }
        },
        cubeOutlineChangeEvent() {
            const cubeOutline = this.$refs.cubeOutline.value;
            if(this.cube) {
                this.cube.children[0].material.color.set(cubeOutline);
            }
        },
        cubeColorChangeEvent() {
            const cubeColor = this.$refs.cubeColor.value;
            if (this.cube) {
                this.cube.material.color.set(cubeColor);
            }
        },
        toggleSessionsButton() {
            this.isSessionToggleOpen = !this.isSessionToggleOpen;

            if (this.isSessionToggleOpen) {
                this.$refs.background.classList.add('blur');
            } else {
                this.$refs.background.classList.remove('blur');
            }
        },
        toggleColorOpenAndEditClose() {
            this.isEditToggleOpen =!this.isEditToggleOpen;
            this.isColorToggleOpen =!this.isColorToggleOpen;
            if (this.isColorToggleOpen) {
                this.$refs.background.classList.add('blur');
            } else {
                this.$refs.background.classList.remove('blur');
            }
        },
        toggleColorOpen() {
            this.isColorToggleOpen = !this.isColorToggleOpen;
            
            if (this.isColorToggleOpen) {
                this.$refs.background.classList.add('blur');
            } else {
                this.$refs.background.classList.remove('blur');
            }
        },
        toggleEditButton() {
            this.isEditToggleOpen =!this.isEditToggleOpen;
            if (this.isEditToggleOpen) {
                this.$refs.background.classList.add('blur');
            } else {
                this.$refs.background.classList.remove('blur');
            }
        },
    },
    beforeDestroy() {
        const view = this.$refs.preview_module_view;
        if (view) {
            view.removeEventListener('mousemove', this.handleMouseMove);
            view.removeEventListener('click', this.hideCursorAndFocus);
            view.removeEventListener('mouseenter', this.hideCursorAndFocus);
        }
    },
    mounted() {
        let view = this.$refs.preview_module_view;
        if (view) {
            view.addEventListener('mousemove', this.handleMouseMove);
            view.addEventListener('click', this.hideCursorAndFocus);
            view.addEventListener('mouseenter', this.hideCursorAndFocus);
        }
        this.hideCursorAndFocus();
        document.addEventListener('click', this.handleDocumentClick);
        let width = window.innerWidth - 600, height = window.innerHeight - 200;

        const camera = new THREE.PerspectiveCamera(70, width / height, 0.01, 10);
        camera.position.z = 1;
        camera.position.x = 1;
        camera.position.y = 1;

        const scene = new THREE.Scene();

        const background_loader = new THREE.TextureLoader();
        background_loader.load('/scene_background.jpg', function(tex) {
            scene.background = tex;
        })

        const geometry = new THREE.BoxGeometry(0.2, 0.2, 0.2);
        const material = new THREE.MeshBasicMaterial({color: 'red'});
        this.cube = new THREE.Mesh(geometry, material);
        scene.add(this.cube);

        const edges = new THREE.EdgesGeometry(geometry);
        const lineMaterial = new THREE.LineBasicMaterial({color: 'black', linewidth: 100})
        const outline = new THREE.LineSegments(edges, lineMaterial);
        this.cube.add(outline); 


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