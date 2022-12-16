<template>
    <div class="keyboard-wrapper">
        <!-- <pre>{{arrayKeys}}</pre> -->
        <div class="row-wrapper" v-for="(row, index) in arrayKeys" :key="index">
        <!-- <pre>{{row}}</pre> -->
            <button 
                v-for="(item, index) in row.value" 
                :key="item + index" 
                :class="keyClass(item)" :disabled="keyClass(item) === 'wrong'"
                @click="emitKeySelection(item.toUpperCase())"
                tabindex="-1"
            >
                {{ item.toUpperCase() }}
            </button>
        </div>
    </div>
</template>

<script setup>

const props = defineProps({
    wrongKeys: Set,
    correctKeys: Set,
    closeKeys: Set,
})

onMounted(() => {
    console.log(wrongKeys)
})

const { wrongKeys, correctKeys, closeKeys } = toRefs(props)

const keys = ['q', 'w', 'e', 'r', 't', 'y', 'u', 'i', 'o', 'p', '←', 'a', 's', 'd', 'f', 'g', 'h', 'j', 'k', 'l', 'ENTER', 'z', 'x', 'c', 'v', 'b', 'n', 'm']

const topRowKeys = computed(() => {
    return keys.filter((k, index) => index < 11)
})
const middleRowKeys = computed(() => {
    return keys.filter((k, index) => index >= 11 && index < 21)
})
const bottonRowKeys = computed(() => {
    return keys.filter((k, index) => index >= 21 && index < 30)
})

const arrayKeys = computed(() => {
    return [topRowKeys, middleRowKeys, bottonRowKeys]
})

const keyClass = computed(() => {
    return (key) => {
        const upperCasedKey = key.toUpperCase()
        if(wrongKeys.value.has(upperCasedKey)) {
            return 'wrong' 
        }
        if(closeKeys.value.has(upperCasedKey)) {
            return 'includes'
        }
        if(correctKeys.value.has(upperCasedKey)) {
            return 'perfect'
        }
        return 'normal'
    }
})

const emit = defineEmits(['selectedKey'])

function emitKeySelection(key) {
    emit('selectedKey', key)
}

</script>

<style lang="scss" scoped>
    .keyboard-wrapper{
        margin: 24px;
        display: flex;
        flex-direction: column;
        align-items: center;
        @media (max-width: $br-tablet){
            margin: 20px 0;
        }
        .row-wrapper{
            display: flex;
            button{
                font-size: 20px;
                color: $light-color;
                font-weight: 600;
                padding: 12px;
                border-radius: 12px;
                margin: 8px;
                cursor: pointer;
                min-width: 60px;
                height: 60px;
                transition: all 0.4s ease;
                @media (max-width: $br-tablet){
                    font-size: 14px;
                    padding: 4px;
                    margin: 2px;
                    min-width: 22px;
                    height: 36px;
                    border-radius: 8px;
                }
                &.perfect{
                    border-color: $light-color;
                    background-color: $perfect;
                    &:hover{
                        background-color: darken($perfect, 10%);
                    }
                    &:active{
                        border-style: inset;
                    }
                }
                &.includes{
                    border-color: $almost-there;
                    background-color: rgba($almost-there, 0.2);
                    &:hover{
                        background-color: rgba($almost-there, 0.1);
                    }
                    &:active{
                        border-style: inset;
                    }
                }
                &.wrong{
                    border-color: $wrong;
                    background-color: rgba($wrong, 0.08);
                    color: rgba($light-color, 0.5);
                    cursor: not-allowed;
                    &:hover{
                        background-color: rgba($wrong, 0.04);
                    }
                    &:active{
                        border-style: inset;
                    }
                }
                &.normal {
                    border: 2px outset rgba($light-color, 0.2);
                    background-color: rgba($light-color, 0.05);
                    &:hover{
                        background-color: rgba($light-color, 0.2);
                    }
                    &:active{
                        border-style: inset;
                    }
                }
            }
        }
    }
</style>