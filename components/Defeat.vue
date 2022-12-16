<template>
    <Modal class="defeat" @close="$emit('close')">
        <span class="vibrate-3">
            😵
        </span>
        <h2>Ops</h2>
        <p>Você não tem mais tentativas!</p>
    </Modal>
</template>
<style lang="scss">
.defeat{
    &.overlay{
        .content{
            border-color: $wrong;
            span{
                position:absolute;
                width: 100%;
                left: 0;
                top: -40px;
            }
        }
    }
}
</style>