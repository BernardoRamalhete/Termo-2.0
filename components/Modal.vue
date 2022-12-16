<template>
    <div class="overlay" @click.self="$emit('close')">
        <div class="content">
            <div class="close-button" @click.self="$emit('close')">❌ Fechar</div>
            <slot/>
        </div>
    </div>
</template>

<script setup>
const emit = defineEmits(['close'])
onMounted(() => {
    window.addEventListener('keydown', closeModal)
})
onBeforeUnmount(() => {
    window.removeEventListener('keydown', closeModal)
})

function closeModal(e) {
    if(e.key === 'Escape') {
        emit('close')
    }
}
</script>

<style lang="scss">
    .overlay {
        position: absolute;
        top: 0;
        left: 0;
        backdrop-filter: blur(4px);
        background-color: #ffffff2a;
        min-width: 100vw;
        min-height: 100vh;
        display: grid;
        place-items: center;
        z-index: 2;
        padding: 24px;
        .content {
            padding: 20px 28px;
            background-color: $dark-color;
            border: 2px solid rgba($light-color, 0.4);
            color: $light-color;
            border-radius: 16px;
            text-align: center;
            position: relative;
            .close-button{
                position: absolute;
                right: 0;
                top: 0;
                font-size: 12px;
                cursor: pointer;
                padding:20px;
                opacity: 0.4;
                transition: all 0.8s ease;
                z-index: 2;
                &:hover{
                    opacity: 1;
                }
            }
            h2 {
                font-size: 28px;
            }
            span{
                font-size: 52px;
            }
        }
    }
</style>