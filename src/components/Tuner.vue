<template>
  <div class="tuner">
    <h1>{{ note }}<sub>{{ octave }}</sub></h1>
    <h3>{{ cents | formatCents }}</h3>
  </div>
</template>

<script>
import PitchFinder from 'pitchfinder'

const NOTES = ['A', 'A#', 'B', 'C', 'C#', 'D', 'D#', 'E', 'F', 'F#', 'G', 'G#']

export default {
  data () {
    return {
      note: 'A',
      octave: 4,
      cents: 0
    }
  },
  mounted () {
    if ('mediaDevices' in navigator) {
      navigator.mediaDevices.getUserMedia({ audio: true })
        .then(stream => {
          const context = new (window.AudioContext || window.webkitAudioContext)()
          const source = context.createMediaStreamSource(stream)
          const processor = context.createScriptProcessor(4096)

          source.connect(processor)
          processor.connect(context.destination)

          const AMDF = PitchFinder.AMDF({
            sampleRate: context.sampleRate,
            minFrequency: 0,
            maxFrequency: 1600,
            sensitivity: 0.3
          })

          processor.onaudioprocess = e => {
            const hz = AMDF(e.inputBuffer.getChannelData(0))

            if (hz) {
              // ¢ or c = 1200 × log2 (f2 / f1)
              let semitones = 12 * Math.log2(hz / 440)
              const cents = semitones * 100

              semitones = Math.round(semitones)

              // eslint-disable-next-line
              const note = NOTES[semitones > 0
                ? semitones % 12
                : semitones % 12 === 0 ? 0 : 12 - Math.abs(semitones % 12)]

              if (note) {
                this.note = note
                this.octave = Math.floor(4 + (9 + semitones) / 12)
                this.cents = cents - semitones * 100
                this.$emit('cents-changed', this.cents)
              }
            }
          }
        })
        .catch(() => { })
    } else {
      this.$emit('unsupported-browser')
    }
  },
  filters: {
    formatCents (cents) {
      cents = cents % 1 === 0 ? cents : cents.toFixed(2)

      if (cents > 0) {
        return `+${cents} ${cents === 1 ? 'cent' : 'cents'}`
      } else {
        return cents + ' cents'
      }
    }
  }
}
</script>

<style scoped>
.tuner {
  align-items: center;
  display: flex;
  flex-direction: column;
  height: 100%;
  justify-content: center;
  overflow: hidden;
}

h1 {
  font-size: 12rem;
  margin: 0;
}

sub {
  font-size: 4rem;
}

h3 {
  font-size: 2rem;
}
</style>
