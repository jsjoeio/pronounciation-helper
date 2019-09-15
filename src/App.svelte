<script>
  export let name;
  export let phrase = "Phrase...";
  export let buttonText = "Start new test";
  export let buttonDisabled = false;
  export const results = {
    text: "Right or wrong?",
    background: "rgba(0, 0, 0, 0.2)"
  };
  var SpeechRecognition = SpeechRecognition || webkitSpeechRecognition;
  var SpeechGrammarList = SpeechGrammarList || webkitSpeechGrammarList;
  var SpeechRecognitionEvent =
    SpeechRecognitionEvent || webkitSpeechRecognitionEvent;
  const phrases = [
    "I love to sing because it's fun",
    "where are you going",
    "can I call you tomorrow",
    "why did you talk while I was talking",
    "she enjoys reading books and playing games",
    "where are you going",
    "have a great day",
    "she sells seashells on the seashore"
  ];

  let phrasePara = document.querySelector(".phrase");
  let resultPara = document.querySelector(".result");
  let diagnosticPara = document.querySelector(".output");

  const testBtn = document.querySelector("button");

  function getRandomNumber(max) {
    const randomNum = Math.floor(Math.random() * max);
    return randomNum;
  }

  function getRandomPhrase(phrases, randomNum) {
    return phrases[randomNum];
  }

  function reset() {
    buttonDisabled = false;
    buttonText = "Start new test";
    phrase = "Phrase...";
    results.text = "Right or wrong?";
    results.background = "rgba(0, 0, 0, 0.2)";
  }

  function testSpeech() {
    // Update button so user knows test is in progress
    buttonDisabled = true;
    buttonText = "Test in progress";

    // Get random phrase
    const max = phrases.length;
    const randomNum = getRandomNumber(max);
    phrase = getRandomPhrase(phrases, randomNum);

    dothething();
  }

  function dothething() {
    const phraseLowerCase = phrase.toLowerCase();
    // To ensure case consistency while checking with the returned output text
    results.background = "rgba(0,0,0,0.2)";
    var grammar =
      "#JSGF V1.0; grammar phrase; public <phrase> = " + phraseLowerCase + ";";
    var recognition = new SpeechRecognition();
    var speechRecognitionList = new SpeechGrammarList();
    speechRecognitionList.addFromString(grammar, 1);
    recognition.grammars = speechRecognitionList;
    recognition.lang = "en-US";
    recognition.interimResults = false;
    recognition.maxAlternatives = 1;

    recognition.start();
    results.text = "Listening...";
    recognition.onresult = function(event) {
      // The SpeechRecognitionEvent results property returns a SpeechRecognitionResultList object
      // The SpeechRecognitionResultList object contains SpeechRecognitionResult objects.
      // It has a getter so it can be accessed like an array
      // The first [0] returns the SpeechRecognitionResult at position 0.
      // Each SpeechRecognitionResult object contains SpeechRecognitionAlternative objects that contain individual results.
      // These also have getters so they can be accessed like arrays.
      // The second [0] returns the SpeechRecognitionAlternative at position 0.
      // We then return the transcript property of the SpeechRecognitionAlternative object
      var speechResult = event.results[0][0].transcript.toLowerCase();
      // diagnosticPara.textContent = "Speech received: " + speechResult + ".";
      if (speechResult === phraseLowerCase) {
        console.log("i heard it correct");
        results.text = "I heard the correct phrase!";
        results.background = "lime";
      } else {
        console.log("that did not sound right");
        results.text = "That didn't sound right.";
        results.background = "red";
      }

      console.log("Confidence: " + event.results[0][0].confidence);
    };

    recognition.onspeechend = function() {
      recognition.stop();
    };

    recognition.onerror = function(event) {
      reset();
      console.error(event.error);
      // diagnosticPara.textContent =
      //   "Error occurred in recognition: " + event.error;
    };

    recognition.onaudiostart = function(event) {
      //Fired when the user agent has started to capture audio.
      console.log("SpeechRecognition.onaudiostart");
    };

    recognition.onaudioend = function(event) {
      //Fired when the user agent has finished capturing audio.
      console.log("SpeechRecognition.onaudioend");
    };

    recognition.onend = function(event) {
      //Fired when the speech recognition service has disconnected.
      console.log("SpeechRecognition.onend");
      setTimeout(reset, 1000);
    };

    recognition.onnomatch = function(event) {
      //Fired when the speech recognition service returns a final result with no significant recognition. This may involve some degree of recognition, which doesn't meet or exceed the confidence threshold.
      console.log("SpeechRecognition.onnomatch");
    };

    recognition.onsoundstart = function(event) {
      //Fired when any sound — recognisable speech or not — has been detected.
      console.log("SpeechRecognition.onsoundstart");
    };

    recognition.onsoundend = function(event) {
      //Fired when any sound — recognisable speech or not — has stopped being detected.
      console.log("SpeechRecognition.onsoundend");
    };

    recognition.onspeechstart = function(event) {
      //Fired when sound that is recognised by the speech recognition service as speech has been detected.
      console.log("SpeechRecognition.onspeechstart");
    };
    recognition.onstart = function(event) {
      //Fired when the speech recognition service has begun listening to incoming audio with intent to recognize grammars associated with the current SpeechRecognition.
      console.log("SpeechRecognition.onstart");
    };
  }
</script>

<style>
  :global(body, html) {
    margin: 0;
  }

  :global(html) {
    height: 100%;
    background-color: teal;
  }

  :global(body) {
    height: inherit;
    overflow: hidden;
  }

  h1,
  p {
    font-family: sans-serif;
    text-align: center;
  }

  div p {
    padding: 20px;
    background-color: rgba(0, 0, 0, 0.2);
  }

  div {
    overflow: auto;
    position: absolute;
    bottom: 0;
    right: 0;
    left: 0;
  }

  button {
    margin: 0 auto;
    display: block;
    font-size: 1.1rem;
    width: 170px;
    line-height: 2;
    margin-top: 30px;
  }

  @media all and (max-height: 410px) {
    div {
      position: static;
    }
  }

  .phrase {
    font-weight: bold;
  }

  .output {
    font-style: italic;
  }
</style>

<h1>Phrase matcher</h1>
<p>Press the button then say the phrase to test the recognition.</p>

<button disabled={buttonDisabled} on:click={testSpeech}>{buttonText}</button>

<div>
  <p class="phrase">{phrase}</p>
  <p class="result" style={`background-color:${results.background}`}>
    {results.text}
  </p>
  <p class="output">...diagnostic messages</p>
</div>
