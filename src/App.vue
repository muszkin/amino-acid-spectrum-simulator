<template>
  <div class="container">
    <!-- Carousel Row -->
    <Carousel :items-to-show="10" class="slider" ref="aminoSlider" @slide-start="updateMap" @slide-end="updateMap">
      <Slide v-for="(item, index) in items" :key="index" :data-index="index" :data-name="item.letter">
        <div class="card" @click="showChoices(index)">
          <div class="content"> {{ item.letter }}</div>
        </div>
        <div v-if="item.showChoices">
          <!-- Display choices in a column -->
          <div v-for="choice in item.choices" :key="choice" @click="selectChoice(index, choice)">
            {{ choice }}
          </div>
        </div>
      </Slide>
    </Carousel>

    <!-- Letters Row -->
    <div>
      <span v-for="(letter, index) in allLetters" :key="index" :class="{ 'center-border-highlight':isCurrent(index),'left-border-highlight': isLeft(index),'right-border-highlight': isRight(index), 'modified': letter.modified }" @click="moveCarouselTo(index)">
        {{ letter.char }}
      </span>
    </div>


  </div>
</template>

<script>
import 'vue3-carousel/dist/carousel.css'
import { ref } from 'vue'
import { Carousel, Slide } from 'vue3-carousel'
export default {
  setup() {
    const aminoSlider = ref(null)
    return { aminoSlider }
  },
  components: {
    Carousel,
    Slide
  },
  data() {
    return {
      items: Array.from({ length: 124 }, (_, i) => ({
        letter: String.fromCharCode(65 + (i % 26)), // A, B, C, etc.
        choices: ['1', '2', '3', '4', '5', '6'],
        showChoices: false,
      })),
      currentIndex: 0
    };
  },
  computed: {
    allLetters() {
      return this.items.map(item => ({ char: item.letter, modified: item.modified || false }));
    }
  },
  methods: {
    showChoices(index) {
      this.items[index].showChoices = !this.items[index].showChoices;
    },
    selectChoice(index, choice) {
      this.items[index].letter = choice;
      this.items[index].modified = true;
      this.items[index].showChoices = false;
    },
    isCurrent(index) {
      return index >= this.currentIndex && index < this.currentIndex + 10;
    },
    isCenter(index) {
      return index === this.currentIndex + 5;
    },
    isLeft(index) {
      return !this.isCurrent(index - 6) && this.isCurrent(index + 5);
    },
    isRight(index) {
      return !this.isCurrent(index + 6) && this.isCurrent(index + 5);
    },
    moveCarouselTo(index) {
      this.currentIndex = index + 5;
      this.$refs.aminoSlider.slideTo(index - 5)
    },
    updateMap(data) {
      this.currentIndex = data.currentSlideIndex;
    }
  }
};
</script>

<style>
.slider {
  margin: 0 auto;
  width: 100%;
  max-width: 1280px;
  overflow: hidden;
  position: relative;
}
.highlight {
  background-color: rgba(84, 175, 255, 0.2);
  border: 1px solid rgba(84, 175, 255, 0.5);
  border-radius: 5px;
}
.right-border-highlight {
  border-top: 1px solid rgba(84, 175, 255, 0.5);
  border-bottom:  1px solid rgba(84, 175, 255, 0.5);
  border-right: 1px solid rgba(84, 175, 255, 0.5);
  border-left: 0px solid rgba(84, 175, 255, 0.5);
}
.left-border-highlight {
  border-top: 1px solid rgba(84, 175, 255, 0.5);
  border-bottom:  1px solid rgba(84, 175, 255, 0.5);
  border-right: 0;
  border-left: 1px solid rgba(84, 175, 255, 0.5);
}
.center-border-highlight {
  border-top: 1px solid rgba(84, 175, 255, 0.5);
  border-bottom:  1px solid rgba(84, 175, 255, 0.5);
  border-right: 0;
  border-left: 0;
}
.modified {
  color: red;
}
.card {
  width: 100px; /* Fixed width */
  height: 140px; /* Fixed height */
  border: 1px solid #000; /* Black border */
  border-radius: 10px; /* Rounded corners */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1); /* Subtle shadow for depth */
  background-color: #fff; /* White background */
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 10px;
}

.content {
  font-size: 2em; /* Large font size for visibility */
  font-weight: bold; /* Bold font for the letter/number */
  color: #333; /* Dark color for the text */
}
.container {
  max-width: 1280px;
}
</style>
