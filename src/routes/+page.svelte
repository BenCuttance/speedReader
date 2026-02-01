<script lang="ts">
  let startSentence =
    "Hello! Please paste your text or upload a pdf to read from!";
  let currentWord: string = $state("");
  let firstHalf: string = $state("");
  let middleLetter: string = $state("");
  let lastHalf: string = $state("");
  let textInput: string = $state("");
  let displayRunID = 0;
  let timeoutId: ReturnType<typeof setTimeout> | null = null;

  function cancelDisplay() {
    displayRunID++;
    if (timeoutId !== null) {
      clearTimeout(timeoutId);
      timeoutId = null;
    }
  }

  function delay(ms: number): Promise<void> {
    return new Promise((resolve) => {
      timeoutId = setTimeout(() => {
        timeoutId = null;
        resolve();
      }, ms);
    });
  }

  function collectTextAreInput() {
    cancelDisplay();
    const value = textInput;
    constructAndDisplay(value);
  }

  function formWordArray(sentence: string) {
    return sentence.trim().split(/\s+/);
  }

  function findMiddleLetter(currentWord: string) {
    const cleaned = currentWord.replace(/[^a-zA-Z0-9]/g, "");
    let middleIndex = Math.floor(cleaned.length / 2);

    firstHalf = currentWord.slice(0, middleIndex);
    middleLetter = currentWord[middleIndex];
    lastHalf = currentWord.slice(middleIndex + 1, currentWord.length);
  }

  async function displayWords(words: string[]) {
    const runId = ++displayRunID;

    for (const word of words) {
      if (runId !== displayRunID) return;

      currentWord = word;
      await delay(2000);
      if (runId !== displayRunID) return;
    }
  }

  function constructAndDisplay(sentence: any) {
    let newWordArray = formWordArray(sentence);
    displayWords(newWordArray);
  }

  constructAndDisplay(startSentence);

  $effect(() => {
    findMiddleLetter(currentWord);
  });
</script>

<div class="wrapper">
  <div class="word">
    <span class="left">{firstHalf}</span>
    <span class="mid">{middleLetter}</span>
    <span class="right">{lastHalf}</span>
  </div>

  <div class="text-upload">
    <textarea bind:value={textInput} name="upload" id="upload-text-area"
    ></textarea>
    <button onclick={collectTextAreInput} id="submit-button">Submit</button>
  </div>
</div>

<style>
  * {
    font-family: Arial, Helvetica, sans-serif;
  }

  .wrapper {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 3rem;
  }

  .word {
    display: grid;
    align-items: center;
    grid-template-columns: 1fr auto 1fr;
    justify-items: center;
    font-size: 4rem;
  }

  .left {
    justify-self: end;
    text-align: right;
  }

  .mid {
    justify-self: center;
    color: red;
  }

  .right {
    justify-self: start;
    text-align: left;
  }

  .text-upload {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 1.25rem;
  }

  #upload-text-area {
    resize: none;
    width: 400px;
    height: 200px;
    padding: 1rem;
    border-radius: 8px;
    border: 1.5px solid black;
    font-family: inherit;
    font-size: 1rem;
    line-height: 1.4;

    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.4);
    transition:
      transform 120ms ease,
      box-shadow 120ms ease,
      border-color 120ms ease;
  }

  #upload-text-area:focus {
    outline: none;
    box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.35);
  }

  #submit-button {
    border: black;
    border-radius: 8px;
    padding: 1rem 2rem;
    background-color: black;
    box-shadow: 2px 2px 4px rgba(0, 0, 0, 0.4);
    color: white;

    transition:
      transform 120ms ease,
      box-shadow 120ms ease;
  }

  #submit-button:hover {
    transform: translate(2px, 2px);
    box-shadow: 1px 1px 3px rgba(0, 0, 0, 0.35);
  }
</style>
