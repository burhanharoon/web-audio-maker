<template>
  <div class="flex flex-col gap-4 p-4">
    <input-label value="Type">
      <select-input v-model="data.type">
        <option value="lowpass">Low-Pass</option>
        <option value="highpass">High-Pass</option>
        <option value="bandpass">Band-Pass</option>
        <option value="lowshelf">Low-Shelf</option>
        <option value="highshelf">High-Shelf</option>
        <option value="peaking">Peaking</option>
        <option value="notch">Notch</option>
        <option value="allpass">All-Pass</option>
      </select-input>
    </input-label>
    <audio-param :disabled="Boolean(node.audioParams.frequency)" v-model="data.frequency" title="Frequency" :default="350" :beats="beats" min="0" max="20000" :values="[262, 277, 294, 311, 330, 349, 370, 392, 415, 440, 466, 494]" />
    <audio-param :disabled="Boolean(node.audioParams.detune)" v-model="data.detune" title="Detune" :default="0" :beats="beats" min="0" max="20000" :values="[262, 277, 294, 311, 330, 349, 370, 392, 415, 440, 466, 494]" />
    <audio-param :disabled="Boolean(node.audioParams.Q)" v-model="data.Q" title="Q Factor" :beats="beats" min="0.0001" max="1000" :default="1" :values="[0.0001, 0.1, 1, 5, 10, 25, 50, 100, 500, 1000]" />
    <audio-param :disabled="Boolean(node.audioParams.gain)" v-model="data.gain" title="Gain" :beats="beats" min="-40" max="40" :default="0" :values="[-40, -30, -20, -10, -5, 0, 5, 10, 20, 30, 40]" />
  </div>
</template>

<script>
  import InputLabel from './InputLabel.vue';
  import SelectInput from './SelectInput.vue';
  import AudioParam from './AudioParam.vue';

  export default {
    components: {
      InputLabel,
      SelectInput,
      AudioParam,
    },

    props: {
      id: {
        default: null,
      },
    },

    data() {
      return {
        node: {
          audioParams: {},
          data: {},
        },
        data: {
          type: 'lowpass',
          frequency: 350,
          detune: 0,
          Q: 1,
          gain: 0,
        },
        beats: 60,
      };
    },
    
    mounted() {
      this.node = this.$store.getters.getNode(this.id);
      if (this.node.data) {
        this.data.type = this.node.data.type ?? 'lowpass';
        this.data.frequency = this.node.data.frequency ?? 350;
        this.data.detune = this.node.data.detune ?? 0;
        this.data.Q = this.node.data.Q ?? 1;
        this.data.gain = this.node.data.gain ?? 0;
      }
      this.beats = this.$store.getters.inheritedBeats(this.node);
    },

    watch: {
      data: {
        handler(value) {
          this.$store.commit('updateNodeData', { id: this.id, data: value });
        },
        deep: true,
      },
    },
  };
</script>
