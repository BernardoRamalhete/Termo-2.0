<template>
    <Modal class="victory" @close="$emit('close')">
        <span class="pop-animation">
            😎
        </span>
        <h2> Vitória! </h2>
        <p>Parabéns, você acertou o termo!</p>
    </Modal>
</template>

<style lang="scss" >
.victory{
    &.overlay{
        .content{
            border-color: $perfect;
        }
    }
}
</style>