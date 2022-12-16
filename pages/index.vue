<template>
  <main>
    <Transition>
        <Victory @close="closeVictoryModal" v-if="showVictoryModal"/>
    </Transition>
    <Transition>
        <Defeat @close="closeDefeatModal" v-if="showDefeatModal"/>
    </Transition>
    <Transition>
        <Invalid @close="closeInvalidModal" v-if="showInvalidModal" :word="invalidWord"/>
    </Transition>
    <Transition>
        <Help @close="closeHelpModal" v-if="showHelpModal"/>
    </Transition>

    <div class="content-wrapper">
        <header>
            <span class="logo">🎯 Termo 2.0</span>
            <span class="how-it-works" @click="openHelpModal">Como funfa?</span>
        </header>
        <div v-if="!showDefeatModal && !showInvalidModal && !showVictoryModal">
            <TransitionGroup name="slide">
                <h2 class="feedback-reaction" v-if="answerQuality === 'good'"> 👏 Bom!</h2>
                <h2 class="feedback-reaction" v-else-if="answerQuality === 'regular'">👌 Quase lá! </h2>
                <h2 class="feedback-reaction" v-else-if="answerQuality !== ''">👎 Essa não deu! </h2>
            </TransitionGroup>
        </div>
        <div class="grid">
            <div 
                v-for="line in 6" 
                :key="line"
                :class="`line line-${line}`"
            >
                <input 
                    type="text" 
                    v-for="input in 5" 
                    @change="(e) => handleInputChange(line, input, e)"
                    @keyup="(e) => handleInputChange(line, input, e)"
                    @click="(e) => preventClick(e, line)"
                    :key="input"
                    maxlength="1"
                    :class="`input input-${input} ${inputsController[line] ? inputClass(line, input) : ''}`"
                    :disabled="inputsController[line]"
                    tabindex="-1"
                />
            </div>
        </div>
        
        <Keyboard @selectedKey="getSelectedKey" :closeKeys="closeLetters" :correctKeys="correctLetters" :wrongKeys="wrongLetters"/>
    </div>
  </main>
</template>

<script setup>
import {word} from '@/words.js'
import { forbidden } from '@/forbiddenAnswers.js'

function handleArrowPress(e) {
    if(e.keyCode == '37') {
        if(document.activeElement.className.includes('input') && !document.activeElement.className.includes('input-1')) {
            const previousInput = document.activeElement.previousSibling
            previousInput.focus()
        }
        
        console.log( )
    }
    if(e.keyCode == '39') {
        if(document.activeElement.className.includes('input') && !document.activeElement.className.includes('input-5')) {
            const nextInput = document.activeElement.nextSibling
            nextInput.focus()
        }
    }
}

onMounted(() => {
    window.addEventListener('keydown', handleArrowPress)
})

onBeforeUnmount(() => {
    window.removeEventListener('keydown', handleArrowPress)
})

const showVictoryModal = ref(false)
function closeVictoryModal() {
    showVictoryModal.value = false
    window.location.reload(true)
}
function openVictoryModal() {
    showVictoryModal.value = true
}

const showDefeatModal = ref(false)
function closeDefeatModal() {
    showDefeatModal.value = false
    window.location.reload(true)
}
function openDefeatModal() {
    showDefeatModal.value = true
}

const showInvalidModal = ref(false)
function closeInvalidModal() {
    let line = null;
    let input = null
    for(let i = 1; i < 7; i++) {
        for(let j = 1; j < 6; j++) {
            if(inputValues[i][j] === '') {
                line = i
                input = j
                break;
            }
        }
        if(line != undefined && input != undefined) {
            break;
        }
    }
    showInvalidModal.value = false
    document.querySelector(`.line-${line}`).querySelector(`.input-${input}`).focus()
}
function openInvalidModal() {
    showInvalidModal.value = true
}

const showHelpModal = ref(true)
function closeHelpModal() {
    showHelpModal.value = false
}
function openHelpModal() {
    showHelpModal.value = true
}

const filterForAccentedWords = new RegExp(/[à-ü]|[À-Ü]/)
const possibleAnswers = word.filter(item => item.length === 5 && !forbidden.includes(item) && !filterForAccentedWords.test(item))

const randomIndex = Math.floor(Math.random() * possibleAnswers.length)

let answer = reactive({})
possibleAnswers[randomIndex].split('').map((letter, index) => {
    answer[index + 1] = letter.toUpperCase()
})

console.log(answer)

const inputClass = computed( () => {
    return (line, input) => {
        let finalValue = ''
        const inputArray = Object.values(inputValues[line]);
        const answerArray = Object.values(answer);
        if(answer[input].toUpperCase() === inputValues[line][input].toUpperCase()) {
            finalValue = 'perfect';
            correctLetters.add(inputValues[line][input].toUpperCase())
            wrongLetters.delete(inputValues[line][input].toUpperCase())
            closeLetters.delete(inputValues[line][input].toUpperCase())
        } else {
            if(answerArray.includes(inputValues[line][input]) && input - 1 === inputArray.lastIndexOf(inputValues[line][input])) {
                finalValue = 'includes'
                correctLetters.delete(inputValues[line][input].toUpperCase())
                wrongLetters.delete(inputValues[line][input].toUpperCase())
                closeLetters.add(inputValues[line][input].toUpperCase())
            }
        } 
        if(finalValue === '') {
            correctLetters.delete(inputValues[line][input].toUpperCase())
            wrongLetters.add(inputValues[line][input].toUpperCase())
            closeLetters.delete(inputValues[line][input].toUpperCase())
        }
        return finalValue
        
    }
}
) 


const inputValues = reactive({
    1: {
        1: '',
        2: '',
        3: '',
        4: '',
        5: ''
    },
    2: {
        1: '',
        2: '',
        3: '',
        4: '',
        5: ''
    },
    3: {
        1: '',
        2: '',
        3: '',
        4: '',
        5: ''
    },
    4: {
        1: '',
        2: '',
        3: '',
        4: '',
        5: ''
    },
    5: {
        1: '',
        2: '',
        3: '',
        4: '',
        5: ''
    },
    6: {
        1: '',
        2: '',
        3: '',
        4: '',
        5: ''
    }
})
const inputsController = reactive({
    1: false,
    2: false,
    3: false,
    4: false,
    5: false,
    6: false,
})


const invalidWord = ref('')
async function validateLine(line) {
    let validLine = true
    const lineWord = Object.values(inputValues[line]).join('')
    const answerWord = Object.values(answer).join('')
    if(lineWord === answerWord) {
        openVictoryModal()
        return;
    }
    if(lineWord.length < 5) {
        validLine = false
    } else {
        let otherResponses = []  
        for(let i = 1; i < line; i++) {
            otherResponses.push(Object.values(inputValues[i]).join(''))
        }
        if(otherResponses.includes(Object.values(inputValues[line]).join(''))) validLine = false
        if(validLine) {
            const response = word.includes(lineWord.toLowerCase())
            if(!response) {
                validLine = false
            }
        }
    }
    if(validLine) {
        getAnswerQuality(line)
        inputsController[line] = true
        if(!showVictoryModal.value && line === 6) {
            openDefeatModal()
        }
    } else {
        invalidWord.value = Object.values(inputValues[line]).join('')
        const lineInputs = [...document.querySelector(`.line-${line}`).querySelectorAll('.input')]
        lineInputs.map((input, index) => {
            input.value = ''
            inputValues[line][index + 1] = ''
        })
        openInvalidModal()
    }
    return validLine
}

function handleInputChange(line, input, e) {
    if(e.key !== 'Enter') {
        const currentLineElement= document.querySelector(`.line-${line}`)
         if(e.target.value !== '' && e.keyCode != '37' && e.keyCode != '39') {
            if(input < 5) {
                currentLineElement.querySelector(`.input-${input + 1}`).focus()
            }
        } else {
            if(e.key === 'Backspace') {
                if(input > 1) {
                    const currentInputElement = currentLineElement.querySelector(`.input-${input}`)
                    if(inputValues[line][input] == '' && input > 1) {
                        const previousInputElement = currentLineElement.querySelector(`.input-${input - 1}`)
                        previousInputElement.focus()
                        previousInputElement.value = ""
                        inputValues[line][input - 1] = ''
                    } else {
                        currentInputElement.value = ''
                        currentInputElement.focus()
                        inputValues[line][input] = ''
                    }
                }
            }
        }
        inputValues[line][input] = e.target.value.toUpperCase()
    } else {
        if(line < 6) {
            document.querySelector(`.line-${line + 1}`).firstElementChild.focus()
            checkInputs(line + 1, document.querySelector(`.line-${line + 1}`).querySelector('.input-1'))
        } else {
            checkInputs(line + 1, null)
        }
    }

}



async function checkInputs(line, element) {
    if(line > 1 && line < 7) {
        if(!await validateLine(line - 1)) {
            element.blur()
        }
    }
    if(line === 7) {
        await validateLine(line - 1)
    }
}


let answerQuality = ref('')

function getAnswerQuality(line) {
    const lineArray = Object.values(inputValues[line])
    const answerArray = Object.values(answer)
    answerQuality.value = ''

    answerArray.map((answerLetter, answerIndex) => {
        if(answerLetter === lineArray[answerIndex]) answerQuality.value = 'good'
    })

    if(answerQuality.value !== 'good') {
        lineArray.map((lineLetter) => {
            if(answerArray.includes(lineLetter.toUpperCase())) answerQuality.value = 'regular'
        })
        if(answerQuality.value !== 'regular') {
            answerQuality.value = 'bad'
        }
    }

    setTimeout(() => {
        answerQuality.value = ''
    }, 2000)

}


function preventClick(e, line) {
    if(line > 1) {
        if(!document.querySelector(`.line-${line - 1}`).querySelector('.input-1').disabled) {
            e.target.blur()
            invalidWord.value = ''
            openInvalidModal()
        }
    }
}

const wrongLetters = reactive(new Set())
const correctLetters = reactive(new Set())
const closeLetters = reactive(new Set())

function getSelectedKey(key) {
    let pressedKey = key

    if(key === "ENTER") pressedKey = 'Enter'

    if(key === "←") pressedKey = 'Backspace'


    for(let i = 1; i < 7; i++) {
        const testingLine = document.querySelector('.line-' + i)

            for(let j = 1; j < 6; j++) {
                const testingInput = testingLine.querySelector('.input-' + j)
    
                if(testingInput.value === "") {
                    if(pressedKey === 'Backspace') {
                        let previousInputElement = null
                        if(j > 1) {
                            previousInputElement = testingLine.querySelector(`.input-${j - 1}`)
                        } else {
                            if(i > 1) {
                                previousInputElement = document.querySelector(`.line-1`).querySelector(`.input-5`)
                            } else {
                                previousInputElement = testingLine.querySelector(`.input-1`)
                            }
                        }
                        if(!previousInputElement.disabled) {
                            previousInputElement.value = ''
                            handleInputChange(i, j, {key: pressedKey, target: previousInputElement})
                        } else {
                            console.log(previousInputElement)
                        }
                        return
                    }

                    if(pressedKey === 'Enter') {
                        if(i < 6) {
                            document.querySelector(`.line-${i}`).firstElementChild.focus()
                            checkInputs(i, document.querySelector(`.line-${i}`).querySelector('.input-1'))
                        } else {
                            checkInputs(i, null)
                        }
                        return
                    }

                    let previousLineElementDisabled = true
                    if(i > 1) {
                        previousLineElementDisabled = document.querySelector(`.line-${i - 1}`).querySelector('.input-1').disabled
                    }
    
                    if(j > 1 || previousLineElementDisabled) {
                        testingInput.value = pressedKey
                        handleInputChange(i, j, {key: pressedKey, target: testingInput})
                    }
                    
                    return
                }
        }

    }
}

</script>

<style lang="scss" scoped>
main {
    display: grid; 
    place-items: center;
    min-height:100vh;
    position: relative;

    .content-wrapper {
        display: flex;
        flex-direction: column;
        align-items: center;
        @media (max-width: $br-tablet){
            margin-top: 80px;
        }
        header {
            color: $light-color;
            position: absolute;
            width: 100%;
            top: 0;
            left: 0;
            padding: 24px;
            display: flex;
            justify-content: space-between;
            align-items:center;
            @media (max-width: $br-tablet){
                padding: 24px 16px;
            }
            
            .logo{
                font-size: 20px;
                font-weight: bold;
            }
            .how-it-works{
                cursor: pointer;
                opacity: 0.8;
                font-weight: bold;
                transition: all 0.4s ease;
                &:hover{
                    opacity: 1;
                }
            }
        }
        .feedback-reaction{
            font-size: 80px;
            position: absolute;
            bottom: 0;
            left:0;
            width:100%;
            text-align:center;
            color: $light-color;
            z-index: 1;
            text-shadow: 0 8px 16px rgba(0,0,0, 0.8);
            @media (max-width: $br-tablet){
                font-size: 62px;
            }
        }

        .line {
            position: relative;
            z-index: 1;
            .input {
                width: 72px;
                height: 72px;
                padding: 8px;
                background-color:rgba($light-color, 0.05);
                border: 2px dashed rgba($light-color, 0.2);
                color: $light-color;

                font-size: 42px;
                font-weight: 600;
                text-transform: uppercase;
                text-align: center;

                margin: 8px;
                border-radius: 16px;
                @media (max-width: $br-tablet){
                    width:52px;
                    height: 52px;
                    margin: 4px;
                    font-size: 20px;
                }
                &:focus{
                    background-color:rgba($light-color, 0.2);
                    border-color: $light-color;
                    outline: none;
                    caret-color: transparent;
                }

                &:disabled  {
                    border:2px solid $wrong;
                    background-color: rgba($wrong, 0.08);
                    color: rgba($light-color, 0.5);
                    &.perfect {
                        background-color: $perfect;
                        border:2px solid  $light-color;
                        color: $light-color;
                    }
                    &.includes {
                        background-color: rgba($almost-there, 0.2);
                        border:2px solid $almost-there;
                        color: $light-color;
                    }
                }
            }
        }
    }
}
</style>
