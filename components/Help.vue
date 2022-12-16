<template>
    <Modal @close="$emit('close')" class="helper">
        <h1>Como jogar?</h1>
        <p>Você tem <strong>6 chances</strong> para acertar o termo correto!</p>
        <p>Pressione enter para validar sua resposta! Lembrando que só são aceitas palavras que existem na língua portuguesa</p>
        <p>A cada validação você terá um retorno indicando... </p>
        <ul class="helper-list">
            <li>
                <div class="example perfect">
                    A
                </div>
                <p>Se o termo final possuir a letra e estiver na posição correta, a mesma apresentará o seguinte estilo.</p>
            </li>
            <li>
                <div class="example includes">A</div>
                <p>Se o termo final possuir a letra mas estiver na posição errada, a mesma apresentará o seguinte estilo.</p>
            </li>
            <li>
                <div class="example wrong">A</div>
                <p>Se o termo final não possuir a letra, a mesma apresentará o seguinte estilo.</p>
            </li>
        </ul>
    </Modal>
</template>

<style lang="scss">
    .helper {
        .content {
            max-width: 600px;
            h1 {
                margin-bottom: 32px;
            }
            .helper-list{
                padding: 0;
                li {
                    list-style: none;
                    display: flex;
                    align-items: center;
                    margin: 30px 0;
                    p {
                        text-align: left;
                        flex-grow: 1;
                        margin-left: 24px;
                        font-size: 16px;
                    }

                    &:first-child {
                        margin-top: 40px;
                    }
                }
            }
        }
    }
    .example {
        display: flex;
        justify-content: center;
        align-items: center;
        flex-shrink: 0;
        width: 72px;
        height: 72px;
        padding: 8px;
        font-size: 42px;
        font-weight: 600;
        border: 2px solid transparent;
        border-radius: 16px;
        p {
            margin: 0;
            padding: 0;
        }
    }
    
    .perfect {
        background-color: $perfect;
        border:2px solid  $light-color;
        color: $light-color;
    }
    .includes {
        background-color: rgba($almost-there, 0.2);
        border:2px solid $almost-there;
        color: $light-color;
    }
    .wrong {
        border:2px solid $wrong;
        background-color: rgba($wrong, 0.08);
        color: rgba($light-color, 0.5);
    }
</style>