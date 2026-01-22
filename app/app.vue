<template>
  <div id="wrapper">
    <div id="editor-wrapper">
      <div id="textArea">
        <div id="delete" class="button" v-on:click="deleteText">지우기</div>
        <textarea v-model="eng" id="engText"></textarea>
      </div>
      <div id="resultArea">
        <div id="copy" class="button" v-on:click="copyText">복사</div>
        <div id="text" v-html="marked.parse(gksdudgks(eng))">
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
  import { marked } from 'marked'
  import gksdud from 'gksdud'
  import { ref } from 'vue'

const eng = ref('')

function gksdudgks(text) {
  let koresult = gksdud(text)
  let engPart = text.split('{')
  let korPart = koresult.split('{')
  for (let i=0; i<korPart.length; i++) {
    if (korPart[i].indexOf('}') > 0 && engPart[i].indexOf('}') > 0) {
      korPart[i] = engPart[i].split('}')[0]+korPart[i].split('}')[1]
    }
  }
  let result = korPart.join('')
  return result
}

function copyText() {
  if (process.client){
    var text = gksdudgks(document.querySelector('#engText').value)
    // Clipboard API를 사용하여 텍스트 복사
    navigator.clipboard.writeText(text)
  }
}

function deleteText() {
  eng.value = ''
  localStorage.setItem('engText', '')
}

if (process.client) {
  if (localStorage.getItem('engText')) {
    eng.value = localStorage.getItem('engText')
  }
  document.querySelector('#engText').addEventListener('input', (e)=> {
    localStorage.setItem('engText', e.target.value)
  })
  
}
</script>
<style>
@font-face {
    font-family: 'IbmPlexSans';
    src: url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_20-07@1.0/IBMPlexSansKR-Regular.woff') format('woff');
    font-weight: normal;
    font-display: swap;
}

@font-face {
    font-family: 'IbmPlexSans';
    src: url('https://cdn.jsdelivr.net/gh/projectnoonnu/noonfonts_20-07@1.0/IBMPlexSansKR-SemiBold.woff') format('woff');
    font-weight: 700;
    font-display: swap;
}

  body, textarea {
    font-size: 18px;
    font-family: 'IbmPlexSans';
  }

  body {
    background-color: #eef1f4;

  }

  * {
    box-sizing: border-box;
  }

  #editor-wrapper {
    display: flex;
    width: 100%;
    gap: 10px;
  }

  #editor-wrapper > div {
    width: calc(50vw - 10px);
  }

  #textArea, #resultArea {
    display: flex;
    flex-direction: column;
    gap: 10px;
  }

  textarea {
    width: calc(50vw - 15px);
    height: calc(100dvh - 2rem - 30px);
    resize: none;
    border: #dddddd 1px solid;
    border-radius: 15px;
    padding: 20px;
    overflow-y: scroll;
  }

  #text {
    width: calc(50vw - 15px);
    height: calc(100dvh - 2rem - 30px);
    border: #dddddd 1px solid;
    border-radius: 15px;
    background-color: #fafdff;
    padding: 20px;
    overflow-y: scroll;
  }

  .button {
    background-color: #24a0ed;
    color: white;
    text-align: center;
    padding: 0.25rem;
    border-radius: 15px;
  }
</style>