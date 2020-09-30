<template>
  <div style="background-color: red;width:100%;height: 100%" :style="finalStyle">

      <div class="flex flex-col" v-show="overlay">
          <div v-for="(gradient,index) in gradients" :key="index" class="flex flex-row items-end">
              <div>
                  <label class="block text-gray-700 text-sm font-bold mb-2">Gradient</label>
                  <div class="relative">
                      <select v-model="gradient.type"
                              class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
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
                  <div v-for="(stop,index) in gradient.stops" class="px-1 flex flex-row max-w-sm items-end" :key="`color-${index}`">
                      <div class="flex flex-col">
                          <input type="color" v-model="stop.color">
                          <input type="number" v-model="stop.alpha"
                                 step="0.01" min="0" max="1"
                                 class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                 style="width: 64px">
                      </div>
                      <div class="flex flex-col">
                          <input type="number"
                                 v-model="stop.startPosition"
                                 step="1" min="0" max="100"
                                 class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                 style="width: 64px">
                          <input type="number"
                                 v-model="stop.endPosition"
                                 step="1" min="0" max="100"
                                 class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                 style="width: 64px">
                      </div>

                  </div>

                  <div>
                      <button @click="addStop(gradient)" class="block bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded" title="Add next color stop">+</button>
                  </div>

                  <div>
                      <button @click="addGradient"
                              v-show="gradients.length - 1 === index"
                              class="block bg-green-500 hover:bg-green-700 text-white font-bold py-1 px-4 rounded"
                              title="Add next gradient">+</button>
                      <button
                          @click="removeGradient(index)"
                          v-show="gradients.length - 1 !== index"
                          class="block bg-red-500 hover:bg-red-700 text-white font-bold py-1 px-4 rounded"
                          title="Remove next gradient">x</button>
                  </div>

                  <div>
                      <button @click="moveGradient(index,'up')"
                              v-show="index !== 0"
                              class="block bg-orange-500 hover:bg-orange-700 text-white font-bold py-1 px-4 rounded"
                              title="Move up">▲</button>
                      <button @click="moveGradient(index,'down')"
                              v-show="(gradients.length > 1) && (gradients.length - 1 !== index)"
                              class="block bg-yellow-500 hover:bg-yellow-700 text-white font-bold py-1 px-4 rounded"
                              title="Move down">▼</button>
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
                          startPosition: null,
                          endPosition: null,
                      },
                      {
                          color: '#9198e5',
                          alpha: 1,
                          startPosition: null,
                          endPosition: null,
                      }
                  ],
              }
          ],
      }
    },
    computed: {
      finalStyle() {
          let finalGradient = {
              background: '',
          };

          this.gradients.forEach(gradient => {
              let colors = this.colors(gradient);

              let positionVar = 'turn';
              let positionNum = gradient.turns;
              if(gradient.useDeg) {
                  positionVar = 'deg';
                  positionNum = gradient.degrees;
              }

              finalGradient.background += `${gradient.type}(${positionNum}${positionVar},${colors}),`;
          })

          finalGradient.background = finalGradient.background.slice(0, -1);
          return finalGradient;
      }
    },
    methods: {
        addStop(gradient) {
            const newStop = {
                color: '#ffffff',
                alpha: 1,
                startPosition: null,
                endPosition: null,
            };

            gradient.stops.push(newStop);
        },
        colors(gradient) {
            let colors = '';

            gradient.stops.forEach(stop => {

                let startPosition = '';
                let endPosition = '';
                if(stop.startPosition !== null) {
                    startPosition = `${stop.startPosition}%`;
                }

                if(stop.endPosition !== null) {
                    endPosition = `${stop.endPosition}%`;
                }

                if(stop.startPosition !==null) {
                    colors += this.convertToRgbaString(stop.color,stop.alpha) + `${startPosition} ${endPosition},`;
                } else {
                    colors += this.convertToRgbaString(stop.color,stop.alpha) + `,`;
                }
            });

            colors = colors.slice(0, -1);

            return colors;
        },
        addGradient() {
            let newGradient = {
                type: 'linear-gradient',
                turns: 0,
                degrees:0,
                useDeg: false,
                stops: [
                    {
                        color: '#ffffff',
                        alpha: 1,
                        startPosition: null,
                        endPosition: null,
                    },
                    {
                        color: '#000000',
                        alpha: 1,
                        startPosition: null,
                        endPosition: null,
                    }
                ],
            };

            this.gradients.push(newGradient);
        },
        removeGradient(index) {
            this.gradients.splice(index,1);
        },
        moveGradient(index,direction) {
            if(direction === 'up') {
                let rows = [this.gradients[index], this.gradients[index - 1]];
                this.gradients.splice(index-1, 2, rows[0], rows[1] );
            } else {
                let rows = [this.gradients[index], this.gradients[index + 1]];
                this.gradients.splice(index, 2, rows[1], rows[0] );
            }
        }
    }
}
</script>
<style>
@import "~normalize.css";
@import './assets/styles/main.css';
@import './assets/styles/tailwind.css';
</style>
