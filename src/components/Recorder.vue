<template>
  <div class="hello">
    <button v-if="!state.recording" href="#" @click="initRecorder" role="button">record</button>
    &nbsp;&nbsp;&nbsp;
    <button v-if="state.recording" class="record-btn" href="#" @click="stopRecording" role="button">stop recording</button>
    <div v-if="state.instructionMessage"><p>{{state.instructionMessage}}</p></div>
    <div v-if="state.successMessage"><p class="success">{{state.successMessage}}</p></div>
    <div v-if="state.errorMessage"><p class="danger">{{state.errorMessage}}</p></div>
    <audio v-if="state.recordedAudio" controls :src="state.recordedAudio" type="audio/mpeg" class="mx-auto">
        Your browser does not support the
        <code>audio</code> element.
      </audio>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from 'vue-class-component';
import { reactive } from 'vue'
import Recorder from "./../lib/recorder";
import convertTimeMMSS from "./../lib/utils";


export default {
  setup() {
    console.log("setup");
    const INSTRUCTION_MESSAGE = "Click on record to start recording.";
    const INSTRUCTION_MESSAGE_STOP = "Click stop to stop recording.";
    const ERROR_MESSAGE = "Failed to use microphone. Please refresh and try again and permit the use of a microphone.";
    const SUCCESS_MESSAGE = "Successfully recorded your story!";
    const SUCCESS_MESSAGE_SUBMIT = "Successfully submitted audio message! Thank you!";
    const ERROR_SUBMITTING_MESSAGE = "Error submitting audio message! Please try again later.";
    const INSTRUCTION_INIT_MESSAGE = "Initializing...";

    const MP3_FORMAT = "mp3";

    const initRecorder = ()=>{
      state.instructionMessage = INSTRUCTION_INIT_MESSAGE;
      state.recordedAudio = undefined
      state.recording = true;
      state.recorder.start();
      state.successMessage = "";
      state.errorMessage = "";
      state.instructionMessage = INSTRUCTION_MESSAGE_STOP;
    }

    const stopRecording = ()=>{
      state.recording = false;
      console.log("stopRecording");
      const rec = state.recorder!;
      rec.stop();
      const recordList = rec!.recordList();
      state.recordedAudio = recordList[recordList.length-1].url;
      console.log("recordedAudio", state.recordedAudio)
      state.recordedBlob = recordList[recordList.length-1].blob;
      console.log("recordedBlob", state.recordedAudio)
      if (state.recordedAudio) {
        state.successMessage = SUCCESS_MESSAGE;
        state.instructionMessage = "";
      }
    }

    const micFailedFunc = ()=>{
      state.recording = false;
      state.instructionMessage = INSTRUCTION_MESSAGE;
      state.errorMessage = ERROR_MESSAGE;
    }

    const myRecorder = new Recorder({
        micFailed: micFailedFunc,
        bitRate: 92,
        sampleRate: 44100,
        format: MP3_FORMAT,
      });

    const state = reactive({
      recording: false,
      recordedAudio: undefined,
      recordedBlob: undefined,
      recorder: myRecorder,
      successMessage: "",
      errorMessage: "",
      instructionMessage: INSTRUCTION_MESSAGE,
    });

    return {state, stopRecording, initRecorder};
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.success{
  color: green;
}

.danger{
  color: red;
}
</style>
