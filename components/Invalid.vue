<template>
    <Modal class="invalid" @close="$emit('close')">
        <span v-if="word !== ''" class="wobble-hor-bottom">
            🤔
        </span>
        <span v-else class="wobble-hor-bottom">
            ☝️
        </span>
        <h2 v-if="word !== ''">Ops, palavra estranha</h2>
        <h2 v-else>Não pule linhas</h2>
        <p v-if="word !== ''">A palavra <strong>{{word}}</strong> não existe ou é invalida.</p>
        <p v-else>Preencha a linha vazia antes de selecionar a próxima linha.</p>
    </Modal>
</template>

<script setup>
    const props = defineProps({
        word: String
    })

    const {word} = toRefs(props)
</script>

<style lang="scss">
.invalid{
    &.overlay{
        .content{
            border-color: $almost-there;
            span{
                width: 100%;
                left: 0;
                top: -40px;
            }
        }
    }
}
</style>