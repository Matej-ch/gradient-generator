<template>
    <div class="grid " :class="showOverlay === true ? 'grid-cols-2' : 'grid-cols-1' ">

        <div class="flex flex-col bg-gray-300 relative divide-y divide-gray-400 overflow-y-scroll h-screen" v-show="showOverlay">
            <div class="p-2"><button @click="addGradient"
                                      class="block bg-green-500 hover:bg-green-700 text-white font-bold py-1 px-4 rounded mr-1 mt-1"
                                      title="Add next gradient">Add gradient</button>
            </div>
            <div v-for="(gradient,index) in gradients" :key="index" class="flex flex-row flex-wrap items-end py-2 px-1 relative">
                <div class="p-1">
                    <label class="block text-gray-700 text-sm font-bold mb-2">Gradient</label>
                    <div class="relative">
                        <select v-model="gradient.type"
                                class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                            <option value="linear-gradient">Linear</option>
                            <option value="repeating-linear-gradient">Repeating linear</option>
                            <option value="radial-gradient">Radial</option>
                            <option value="repeating-radial-gradient">Repeating radial</option>
                            <option value="conic-gradient">Conic (chrome only)</option>
                        </select>
                        <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                            <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                        </div>
                    </div>
                </div>

                <div v-if="gradient.type === 'radial-gradient'|| gradient.type === 'repeating-radial-gradient' || gradient.type === 'conic-gradient'" class="flex flex-col justify-start p-1" style="align-items: flex-end;">

                    <div class="flex flex-col" v-if="gradient.type === 'radial-gradient'|| gradient.type === 'repeating-radial-gradient'">
                        <div class="flex flex-row items-center">
                            <label class="block text-gray-700 text-sm font-bold">Size</label>
                            <input type="number"
                                   v-model="gradient.sizeX"
                                   step="1" min="0"
                                   :max=100
                                   class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                   style="width: 64px">
                            <input type="number"
                                   v-model="gradient.sizeY"
                                   step="1" min="0"
                                   :max=100
                                   class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                   style="width: 64px">
                            <a href="#"
                               @click.prevent="gradient.sizeUnit === '%' ? gradient.sizeUnit = 'px' : gradient.sizeUnit = '%'"
                               class="block bg-gray-500 hover:bg-gray-700 text-white font-bold py-1 px-4 rounded">
                                {{gradient.sizeUnit}}
                            </a>
                        </div>
                    </div>

                    <div class="flex flex-col" v-if="gradient.type === 'radial-gradient'|| gradient.type === 'repeating-radial-gradient' || gradient.type === 'conic-gradient'">
                        <div class="flex flex-row items-center">
                            <label class="block text-gray-700 text-sm font-bold">Position</label>
                            <input type="number"
                                   v-model="gradient.positionX"
                                   step="1" min="0"
                                   :max=100
                                   class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                   style="width: 64px">
                            <input type="number"
                                   v-model="gradient.positionY"
                                   step="1" min="0"
                                   :max=100
                                   class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                   style="width: 64px">
                            <a href="#"
                               @click.prevent="gradient.positionUnit === '%' ? gradient.positionUnit = 'px' : gradient.positionUnit = '%'"
                               class="block bg-gray-500 hover:bg-gray-700 text-white font-bold py-1 px-4 rounded">
                                {{gradient.positionUnit}}
                            </a>
                        </div>
                    </div>

                </div>

                <div v-else class="flex flex-row justify-start p-1" style="align-items: flex-end;">
                    <div style="max-width: 100px">
                        <label class="block text-gray-700 text-sm font-bold mb-2">{{gradient.useDeg === true ? 'Degrees' : 'Turns'}}</label>
                        <input
                            type="number"
                            :step="gradient.turns ? 0.01 : 1"
                            min=0
                            :max="gradient.turns ? 1 : 360"
                            v-model="gradient.turns"
                            class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline">
                    </div>
                    <div>
                        <a href="#" @click.prevent="gradient.useDeg = !gradient.useDeg;" class="block bg-gray-500 hover:bg-gray-700 text-white font-bold py-1 px-4 rounded">{{gradient.useDeg === true ? 'T' : 'D'}}</a>
                    </div>
                </div>

                <div class="flex flex-row items-end flex-wrap pt-2">
                    <div v-for="(stop,index) in gradient.stops" class="p-1 flex flex-row max-w-sm items-end" :key="`color-${index}`">
                        <div class="flex flex-col">
                            <div>
                                <label title="Color" class="font-bold text-gray-600">C: </label>
                                <input type="color" v-model="stop.color">
                            </div>

                            <div>
                                <label title="Alpha" class="font-bold text-gray-600">A: </label>
                                <input type="number" v-model="stop.alpha"
                                       step="0.01" min="0" max="1"
                                       class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                       style="width: 64px">
                            </div>
                        </div>

                        <div class="flex flex-row">
                            <div class="flex flex-col">
                                <div class="pl-1">
                                    <label title="Start position" class="font-bold text-gray-600">Sp: </label>
                                    <input type="number"
                                           v-model="stop.startPosition"
                                           step="1" min="0"
                                           :max="stop.useDeg ? 100 : (gradient.type === 'conic-gradient' ? 360 : windowWidth)"
                                           class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                           style="width: 64px">
                                </div>

                                <div class="pl-1">
                                    <label title="End position" class="font-bold text-gray-600">Ep: </label>
                                    <input type="number"
                                           v-model="stop.endPosition"
                                           step="1" min="0"
                                           :max="stop.useDeg ? 100 : (gradient.type === 'conic-gradient' ? 360 : windowWidth)"
                                           class="shadow appearance-none border rounded w-full py-1 px-1 text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
                                           style="width: 64px">
                                </div>

                            </div>
                            <div class="flex flex-col self-end">
                                <a href="#"
                                   @click.prevent="stop.useDeg = !stop.useDeg;"
                                   class="block bg-gray-500 hover:bg-gray-700 text-white font-bold py-1 px-4 rounded">
                                    {{stop.useDeg === true ? '%' :  (gradient.type === 'conic-gradient' ? 'D' : 'px')}}
                                </a>
                            </div>
                        </div>
                    </div>

                    <div class="p-1">
                        <button @click="addStop(gradient)" class="block bg-blue-500 hover:bg-blue-700 text-white font-bold py-1 px-4 rounded" title="Add next color stop">+</button>
                    </div>
                </div>

                <div class="absolute flex flex-row top-0 right-0 p-1">

                    <button @click="moveGradient(index,'up')"
                            class="block text-white font-bold py-1 px-4 rounded mr-1"
                            :class="{'bg-gray-500 pointer-events-none': index === 0, 'bg-orange-500 hover:bg-orange-700': index !== 0}"
                            title="Move up">▲</button>

                    <button @click="moveGradient(index,'down')"
                            class="block text-white font-bold py-1 px-4 rounded mr-1"
                            :class="{'bg-gray-500 pointer-events-none': (gradients.length <= 1) || (gradients.length - 1 === index), 'bg-yellow-500 hover:bg-yellow-700': (gradients.length > 1) && (gradients.length - 1 !== index)}"
                            title="Move down">▼</button>

                    <button
                        @click="removeGradient(index)"
                        class="block text-white font-bold py-1 px-2 rounded"
                        :class="{'pointer-events-none bg-gray-500': index === 0, 'bg-red-500 hover:bg-red-700': index !== 0 }"
                        title="Remove gradient">
                        <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" stroke-width="1.5"
                             stroke="#f3f5f7" fill="none" stroke-linecap="round" stroke-linejoin="round">
                            <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                            <line x1="4" y1="7" x2="20" y2="7" />
                            <line x1="10" y1="11" x2="10" y2="17" />
                            <line x1="14" y1="11" x2="14" y2="17" />
                            <path d="M5 7l1 12a2 2 0 0 0 2 2h8a2 2 0 0 0 2 -2l1 -12" />
                            <path d="M9 7v-3a1 1 0 0 1 1 -1h4a1 1 0 0 1 1 1v3" />
                        </svg>
                    </button>
                </div>
            </div>
        </div>

        <div class="h-screen relative" :style="finalStyle" ref="gradientDiv" id="gradientDiv">
            <a href="#" @click.prevent="showOverlay = !showOverlay" class="absolute right-0 opacity-50" title="Show / Hide overlay" v-if="!createScreenshot" data-html2canvas-ignore>
                <svg v-show="showOverlay" xmlns="http://www.w3.org/2000/svg" width="36"
                     height="36" viewBox="0 0 24 24" stroke-width="1.5" stroke="#212121" fill="none" stroke-linecap="round" stroke-linejoin="round">
                    <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                    <polyline points="16 4 20 4 20 8" /><line x1="14" y1="10" x2="20" y2="4" />
                    <polyline points="8 20 4 20 4 16" /><line x1="4" y1="20" x2="10" y2="14" />
                    <polyline points="16 20 20 20 20 16" /><line x1="14" y1="14" x2="20" y2="20" />
                    <polyline points="8 4 4 4 4 8" /><line x1="4" y1="4" x2="10" y2="10" />
                </svg>

                <svg v-show="!showOverlay" xmlns="http://www.w3.org/2000/svg"
                     width="44" height="44" viewBox="0 0 24 24" stroke-width="1.5" stroke="#607D8B" fill="none" stroke-linecap="round" stroke-linejoin="round">
                    <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                    <polyline points="5 9 9 9 9 5" /><line x1="3" y1="3" x2="9" y2="9" />
                    <polyline points="5 15 9 15 9 19" /><line x1="3" y1="21" x2="9" y2="15" />
                    <polyline points="19 9 15 9 15 5" /><line x1="15" y1="9" x2="21" y2="3" />
                    <polyline points="19 15 15 15 15 19" /><line x1="15" y1="15" x2="21" y2="21" />
                </svg>
            </a>

            <a href="#" class="opacity-75 absolute right-0 bottom-0"  @click.prevent="showCode = !showCode" title="Show code"  v-if="!createScreenshot" data-html2canvas-ignore>
                <svg xmlns="http://www.w3.org/2000/svg" width="34" height="34" viewBox="0 0 24 24" stroke-width="1.5" stroke="#607D8B" fill="none" stroke-linecap="round" stroke-linejoin="round">
                    <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                    <path d="M14 3v4a1 1 0 0 0 1 1h4" />
                    <path d="M17 21h-10a2 2 0 0 1 -2 -2v-14a2 2 0 0 1 2 -2h7l5 5v11a2 2 0 0 1 -2 2z" />
                    <path d="M10 13l-1 2l1 2" />
                    <path d="M14 13l1 2l-1 2" />
                </svg>
            </a>

            <div v-show="showCode" class="absolute inset-x-auto bottom-0 bg-gray-300 rounded right-0 left-0 overflow-auto m-auto w-1/3 h-24">
                <a href="#"
                   @click.prevent="copyStyle"
                   ref="copyBtn">
                    <svg v-if="!isCopied" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" stroke-width="1.5" stroke="#607D8B" fill="none" stroke-linecap="round" stroke-linejoin="round">
                        <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                        <path d="M9 5h-2a2 2 0 0 0 -2 2v12a2 2 0 0 0 2 2h10a2 2 0 0 0 2 -2v-12a2 2 0 0 0 -2 -2h-2" />
                        <rect x="9" y="3" width="6" height="4" rx="2" />
                    </svg>
                    <svg v-else xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" stroke-width="1.5" stroke="#607D8B" fill="none" stroke-linecap="round" stroke-linejoin="round">
                        <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                        <path d="M9 5H7a2 2 0 0 0 -2 2v12a2 2 0 0 0 2 2h10a2 2 0 0 0 2 -2V7a2 2 0 0 0 -2 -2h-2" />
                        <rect x="9" y="3" width="6" height="4" rx="2" />
                        <path d="M9 14l2 2l4 -4" />
                    </svg>
                </a>
                <pre v-show="showCode" class="py-2 px-2 w-full text-xs whitespace-pre-wrap" ref="textToCopy">.gradient {{finalStyle | convertToCss}}</pre>
            </div>

            <a href="#" @click.prevent="createImage" class="opacity-75 absolute left-0 bottom-0" title="Create Image" v-if="!createScreenshot" data-html2canvas-ignore>
                <svg xmlns="http://www.w3.org/2000/svg" width="34" height="34" viewBox="0 0 24 24" stroke-width="1.5" stroke="#607D8B" fill="none" stroke-linecap="round" stroke-linejoin="round">
                    <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                    <line x1="15" y1="8" x2="15.01" y2="8" />
                    <rect x="4" y="4" width="16" height="16" rx="3" />
                    <path d="M4 15l4 -4a3 5 0 0 1 3 0l5 5" />
                    <path d="M14 14l1 -1a3 5 0 0 1 3 0l2 2" />
                </svg>
            </a>

        </div>
    </div>
</template>

<script>

import {hexToRgbaMixin} from "@/mixins/hexToRgbaMixin";
import html2canvas from "html2canvas";

export default {
    name: 'App',
    mixins: [hexToRgbaMixin],
    components: {},
    data() {
      return {
          showOverlay:true,
          showCode: false,
          isCopied: false,
          createScreenshot: false,
          gradients: [
              {
                  type: 'linear-gradient',
                  turns: 0,
                  degrees:0,
                  useDeg: false,
                  positionX: 50,
                  positionY: 50,
                  sizeX: 20,
                  sizeY: 50,
                  positionUnit: '%',
                  sizeUnit: '%',
                  stops: [
                      {
                          color: '#e66465',
                          alpha: 1,
                          startPosition: null,
                          endPosition: null,
                          useDeg: true,
                      },
                      {
                          color: '#9198e5',
                          alpha: 1,
                          startPosition: null,
                          endPosition: null,
                          useDeg: true,
                      }
                  ],
              }
          ],
          windowWidth:window.innerWidth,
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

              if(gradient.type === 'conic-gradient') {
                  let position = `${gradient.positionX}${gradient.positionUnit} ${gradient.positionY}${gradient.positionUnit}`;
                  finalGradient.background += `${gradient.type}(from ${positionNum}${positionVar} at ${position},${colors}),`;

              } else if(gradient.type === 'radial-gradient' || gradient.type === 'repeating-radial-gradient') {
                  let size = `${gradient.sizeX}${gradient.sizeUnit} ${gradient.sizeY}${gradient.sizeUnit}`;
                  let position = `${gradient.positionX}${gradient.positionUnit} ${gradient.positionY}${gradient.positionUnit}`;

                  finalGradient.background += `${gradient.type}(${size} at ${position},${colors}),`;
              } else {

                  finalGradient.background += `${gradient.type}(${positionNum}${positionVar},${colors}),`;
              }
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
                useDeg: true,
            };

            gradient.stops.push(newStop);
        },
        colors(gradient) {
            let colors = '';

            gradient.stops.forEach(stop => {

                let startPosition = '';
                let endPosition = '';
                let unit = 'px';
                if(stop.useDeg) {
                    unit = '%';
                }

                if(gradient.type === 'conic-gradient' && !stop.useDeg) {
                    unit = 'deg';
                }

                if(stop.startPosition !== null) {
                    startPosition = `${stop.startPosition}${unit}`;
                }

                if(stop.endPosition !== null) {
                    endPosition = `${stop.endPosition}${unit}`;
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
                positionX: 50,
                positionY: 50,
                sizeX: 20,
                sizeY: 50,
                positionUnit: '%',
                sizeUnit: '%',
                stops: [
                    {
                        color: '#ffffff',
                        alpha: 1,
                        startPosition: null,
                        endPosition: null,
                        useDeg: true,
                    },
                    {
                        color: '#000000',
                        alpha: 1,
                        startPosition: null,
                        endPosition: null,
                        useDeg: true,
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
        },
        copyStyle() {
            const selection = window.getSelection();
            const range = document.createRange();
            let node = this.$refs.textToCopy;
            range.selectNodeContents(node);
            selection.removeAllRanges();
            selection.addRange(range);
            document.execCommand('copy');
            selection.removeAllRanges();

            this.isCopied = true;

            setTimeout(() => {
                this.isCopied = false;
            }, 1200);
        },
        createImage() {
            this.showOverlay = false;
            this.createScreenshot = true;

            html2canvas(document.getElementById(this.$refs.gradientDiv.getAttribute('id')))
                .then(canvas => {
                    document.body.appendChild(canvas)
                    canvas.style.cssText = 'position: fixed;top: 0;left: 0;opacity: 1;transform: scale(0.8); z-index: 99999999;transition: transform 0.3s cubic-bezier(0.42, 0, 0.58, 1) 0s, opacity 0.3s cubic-bezier(0.42, 0, 0.58, 1) 0s, transform 0.3s cubic-bezier(0.42, 0, 0.58, 1) 0s;box-shadow: rgba(0, 0, 0, 0.3) 6px 5px 12px 4px;';
                    canvas.id = 'canvas'
                });


            setTimeout(() => {
                this.showOverlay = true;
                this.createScreenshot = false;
            }, 3000);

            setTimeout(() => {
                let canvas = document.getElementById('canvas');
                let dataURL = canvas.toDataURL('image/png');
                window.open(dataURL,'GradientWindow');
                canvas.remove();
            }, 500);
        }
    }
}
</script>
<style>
@import "~normalize.css";
@import './assets/styles/main.css';
@import './assets/styles/tailwind.css';
</style>
