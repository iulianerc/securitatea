<template>
  <div id="app">
    <table>
      <tr>
        <th :colspan="alphabet.length">Alfabetul</th>
      </tr>
      <tr>
        <td :key="index" v-for="(letter, index) in alphabet">{{ letter }}</td>
      </tr>
      <tr>
        <td :key="index" v-for="(letter, index) in alphabet">{{ index }}</td>
      </tr>
    </table>
    <br>
    <br>
    <label for="word">
      Word:
      <input id="word" type="text" @input="setWord" :value="word">
    </label>
    <span style="display:inline-block;width: 40px;"></span>
    <label for="defaultWords">
      <select id="defaultWords" @change="setWord">
        <option selected disabled>Default variants</option>
        <option :key="index" v-for="(word, index) in defaultWords" :value="word">{{word.toUpperCase()}}</option>
      </select>
    </label>
    <br>
    <br> <br>
    <br>
    <label for="key">
      Key:
      <input id="key" type="text" v-model="key">
      <span style="color: red; font-size: 12px;">
        * Important: delimiter must be (<span style="font-size: 20px;">,</span>)
      </span>
    </label>
    <br>
    <br>
    <table>
      <tr>
        <td>Cheia repetata</td>
        <td :key="index" v-for="(word, index) in wordArr">{{ keyArr[index % keyArr.length] }}</td>
      </tr>
      <tr>
        <td>Mesajul</td>
        <td :key="index" v-for="(letter, index) in wordArr">{{ letter }}</td>
      </tr>
      <tr>
        <td>Pozitia</td>
        <td :key="index" v-for="(letter, index) in wordArr">{{ getLetterIndex(letter) }}</td>
      </tr>
      <tr>
        <td>Pozitia Criptata</td>
        <td :key="index" v-for="(letter, index) in wordArr">{{ getEncryptedKey(letter, index) }}</td>
      </tr>
      <tr>
        <td>Mesajul criptat</td>
        <td :key="index" v-for="(encryptedLetter, index) in encryptedMessageArr">{{ encryptedLetter }}</td>
      </tr>
    </table>
    <br>
    <br>
    Encrypted message: {{encryptedMessageArr.join('')}}
  </div>
</template>

<script>
import alphabetRo from './assets/alphabets/ro.json'

export default {
  name: 'App',
  data: () => ({
    alphabet: [],
    word: '',
    key: '',
    defaultWords: [
      'criptograma',
      'sau',
      'textul',
      'cifrat',
      'reprezintă',
      'un',
      'mesaj',
      'neinteligibil'
    ]
  }),
  mounted() {
    this.setAlphabet();
  },
  computed: {
    keyArr() {
      return this.key.split(',').filter((key) => key)
    },
    wordArr() {
      return this.word.split('')
    },
    keyRowArr() {
      return this.word
    },
    encryptedMessageArr() {
      return this.wordArr.map((letter,index) => {
        return this.alphabet[this.getEncryptedKey(letter, index)]
      })
    }
  },
  methods: {
    setAlphabet() {
      this.alphabet = alphabetRo
    },
    getLetterIndex(letter) {
      const index = this.alphabet.findIndex((alphabetLetter) => {
        return letter.toUpperCase() === alphabetLetter
      });
      
      return index < 0 ? undefined : index;
    },
    getEncryptedKey(wordLetter, wordIndex) {
      const position = Number(this.getLetterIndex(wordLetter))
      const encryptedPosition = Number(this.keyArr[wordIndex % this.keyArr.length])
      const encryptedKey = (position + encryptedPosition) % this.alphabet.length
      
      return isNaN(encryptedKey) ? undefined : encryptedKey;
    },
    setWord(event) {
      const inputWord = event.target.value.toUpperCase()
      if (!this.alphabet.includes(inputWord[inputWord.length - 1])) {
        this.word = inputWord.slice(0, -1).toUpperCase();
        event.target.value = this.word
        
        return;
      }
      
      this.word = inputWord
    }
  }
}
</script>

<style>
table {
  width: 100%;
  border-collapse: collapse;
}

table, td, th {
  border: solid black 1px;
}

td, th {
  vertical-align: center;
  text-align: center;
  padding: 10px;
}
</style>
