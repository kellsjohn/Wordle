<template>
    <div class="w-full h-screen flex justify-center items-center bg-black overflow-y-hidden">
        <div class="relative w-32 h-48 bg-blue-500 rounded-lg shadow-lg cursor-pointer" ref="card"
            @mousedown="dragCard" />
        <div ref="trailContainer" class="absolute top-0 left-0"></div>
    </div>
</template>

<script setup>

import { ref } from 'vue';

const card = ref(undefined);
const trailContainer = ref(undefined);
let startX = 0,
    startY = 0;

const dragCard = (e) => {
    const cardElement = card.value;

    const rect = cardElement.getBoundingClientRect();
    startX = e.clientX - rect.left;
    startY = e.clientY - rect.top;

    const onMouseMove = (event) => {
        const x = event.clientX - startX;
        const y = event.clientY - startY;

        cardElement.style.position = "fixed";
        cardElement.style.left = `${x}px`;
        cardElement.style.top = `${y}px`;

        createTrail(event.clientX, event.clientY);
    };

    const onMouseUp = () => {
        window.removeEventListener("mousemove", onMouseMove);
        window.removeEventListener("mouseup", onMouseUp);
    };

    window.addEventListener("mousemove", onMouseMove);
    window.addEventListener("mouseup", onMouseUp);
};

const createTrail = (x, y) => {
    const trail = document.createElement("div");
    trail.style.position = "fixed";
    trail.style.left = `${x}px`;
    trail.style.top = `${y}px`;
    trail.style.width = "10px";
    trail.style.height = "10px";
    trail.style.backgroundColor = "rgba(59, 130, 246, 0.7)";
    trail.style.borderRadius = "50%";
    trail.style.pointerEvents = "none";
    trail.style.transition = "opacity 0.5s ease-out, transform 0.5s ease-out";

    trailContainer.value.appendChild(trail);
    requestAnimationFrame(() => {
        trail.style.opacity = "0";
        trail.style.transform = "scale(2)";
    });

    setTimeout(() => {
        trail.remove();
    }, 500);
};
</script>