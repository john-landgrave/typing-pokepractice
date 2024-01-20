<template>
    <div @click="input?.focus()" class="h-[182px]">
        <div class="flex flex-row justify-center items-center">
            <img class="h-[150px]" :src="imageURL">
            <span @click="sayName" class="volume-icon"><i class="fas fa-volume-up"></i></span>
        </div>
        <div class="text-2xl h-[32px]">
            <span v-for="char in printedAnswer" :class="{'correct': char.isCorrect, 'incorrect': !char.isCorrect}">{{ char.char }}</span>
        </div>
        <input class="opacity-0" ref="input" type="text" v-model="rawAnswer" @keyup.enter="emit('next')">
    </div>
</template>

<script lang="ts" setup>
import * as P from 'pokeapi-js-wrapper';
import { Ref, computed, onMounted, ref, watchEffect } from 'vue';

const props = defineProps<{
    no: number;
}>();

const emit = defineEmits<{
    (e: 'done', isCorrect: boolean): void;
    (e: 'next'): void;
}>();

type AnswerCharacter = {
    char: string;
    isCorrect: boolean;
}

const rawAnswer = ref('');
const input: Ref<HTMLInputElement|null> = ref(null);

const p = new P.Pokedex();
const pokemon = await p.getPokemonByName(props.no);

const imageURL = pokemon?.sprites.front_default;
const name = pokemon?.name.toLowerCase();

const s = window.speechSynthesis;

const printedAnswer = computed<AnswerCharacter[]>(() => {
    return Array.from(rawAnswer.value.toLowerCase()).map((char, index) => {
        return {
            char: rawAnswer.value[index],
            isCorrect: char === name[index],
        };
    })
});

watchEffect(() => {
    if (printedAnswer.value.length === name.length && printedAnswer.value.reduce((agg, curr) => agg && curr.isCorrect, true)) {
        emit('done', true);
    } else {
        emit('done', false);
    }
})

const sayName = () => {
    const voice = s.getVoices()[0];
    const utterance = new SpeechSynthesisUtterance(name);
    utterance.voice = voice;
    s.speak(utterance);
}

onMounted(() => {
    input.value?.focus();
})
</script>

<style lang="css" scoped>
    img {
        width: 150px;
        filter: contrast(0) brightness(0%);
    }

    .volume-icon {
        font-size: 24px;
    }

    .correct {
        color: green;
    }

    .incorrect {
        color: red;
    }
</style>