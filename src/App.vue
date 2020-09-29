<template>
  <div style="background-color: red;width:100%;height: 100%" :style="finalStyle">

      <div class="flex flex-col" v-show="overlay">
          <div v-for="(gradient,index) in gradients" :key="index" class="flex flex-row">
              <div>
                  <label class="block text-gray-700 text-sm font-bold mb-2">Gradient</label>
                  <div class="relative">
                      <select v-model="gradient.type"
                              class="block appearance-none w-full bg-blue-100 border border-blue-200 text-gray-700 py-1 px-1 pr-8 rounded-sm leading-tight focus:outline-none focus:bg-white focus:border-gray-500">
                          <option value="linear-gradient">Linear</option>
                          <option value="repeating-linear-gradient">Repeating linear</option>
                          <option value="radial-gradient">Radial</option>
                          <option value="repeating-radial-gradient">Repeating radial</option>
                      </select>
                      <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                          <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                      </div>
                  </div>
              </div>
              <div class="flex flex-row justify-start" style="align-items: flex-end;">
                  <div>
                      <label class="block text-gray-700 text-sm font-bold mb-2">{{gradient.useDeg === true ? 'Degrees' : 'Turns'}}</label>
                      <input
                          v-if="gradient.useDeg"
                          type="number"
                          :step=1
                          min=0
                          :max=360
                          v-model=" gradient.degrees"
                          class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                      <input
                          v-else
                          type="number"
                          :step=0.01
                          min=0
                          :max=1
                          v-model="gradient.turns"
                          class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                  </div>
                  <div>
                      <a href="#" @click.prevent="gradient.useDeg = !gradient.useDeg;" class="block bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded">{{gradient.useDeg === true ? 'T' : 'D'}}</a>
                  </div>
              </div>

              <div class="flex flex-row items-end">
                  <div v-for="(stop,index) in gradient.stops" class="px-1 flex flex-col max-w-sm" :key="`color-${index}`">
                      <input type="color" v-model="stop.color">
                      <input type="number" v-model="stop.alpha"
                             step="0.01" min="0" max="1"
                             class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                             style="width: 64px">
                  </div>
                  <div>
                      <button @click="addStop(gradient)" class="block bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded">+</button>
                  </div>
              </div>

          </div>
      </div>
  </div>
</template>

<script>

import {hexToRgbaMixin} from "@/mixins/hexToRgbaMixin";

export default {
    name: 'App',
    mixins: [hexToRgbaMixin],
    components: {},
    data() {
      return {
          overlay:true,
          gradients: [
              {
                  type: 'linear-gradient',
                  turns: 0,
                  degrees:0,
                  useDeg: false,
                  stops: [
                      {
                          color: '#e66465',
                          alpha: 1,
                          startPosition: '',
                          endPosition: '',
                      },
                      {
                          color: '#9198e5',
                          alpha: 1,
                          startPosition: '',
                          endPosition: '',
                      }
                  ],
              }
          ],
      }
    },
    computed: {
      finalStyle() {
          let finalGradient = {};

          this.gradients.forEach(gradient => {
              let colors = '';
              gradient.stops.forEach(stop => {
                  colors += this.convertToRgbaString(stop.color,stop.alpha) + ',';
              });

              colors = colors.slice(0, -1);

              let positionVar = 'turn';
              let positionNum = gradient.turns;
              if(gradient.useDeg) {
                  positionVar = 'deg';
                  positionNum = gradient.degrees;
              }

              finalGradient.background = `${gradient.type}(${positionNum}${positionVar},${colors})`;
          })

          return finalGradient;
      }
    },
    methods: {
        addStop(gradient) {
            const newStop = {
                color: '#ffffff',
                alpha: 1,
                startPosition: '',
                endPosition: '',
            };
            gradient.stops.push(newStop);
        }
    }
}
</script>

<style>
@import "~normalize.css";
@import './assets/styles/main.css';
@import './assets/styles/tailwind.css';
</style>
