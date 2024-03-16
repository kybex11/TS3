<template>
    <div class="container">
        <div class="elements-container">
            <h1>Inspect</h1>
        </div>
        <div class="preview-container">
            <Preview />
        </div>

    </div>
</template>
<style>
.container {
    display: flex;
    height: 100%;
}
.elements-container {
    text-align: left;
}

.preview-container {
    flex: 3;
    align-items: center;
    justify-content: center;
    display: flex;
}
</style>