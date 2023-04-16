<template>
  <div>
    <div class="space-y-12">
      <div ref="dropContainer" class="border-b border-gray-900/10 pb-12">
        <h2 class="mt-1 text-base font-semibold leading-7 text-gray-900">
          Редактор изображений
        </h2>
        <p class="mt-1 text-sm leading-6 text-gray-600">
          Добавляйте надписи или фигуры
        </p>

        <div class="mt-10 grid grid-cols-1 gap-x-6 gap-y-8">
          <div class="col-span-full">
            <label
              for="cover-photo"
              class="block text-sm font-medium leading-6 text-gray-900"
              >Photo</label
            >
            <div
              class="flex justify-center rounded-lg border border-dashed border-gray-900/25 px-6 py-10"
            >
              <div class="text-center">
                <svg
                  class="mx-auto h-12 w-12 text-gray-300"
                  viewBox="0 0 24 24"
                  fill="currentColor"
                  aria-hidden="true"
                >
                  <path
                    fill-rule="evenodd"
                    d="M1.5 6a2.25 2.25 0 012.25-2.25h16.5A2.25 2.25 0 0122.5 6v12a2.25 2.25 0 01-2.25 2.25H3.75A2.25 2.25 0 011.5 18V6zM3 16.06V18c0 .414.336.75.75.75h16.5A.75.75 0 0021 18v-1.94l-2.69-2.689a1.5 1.5 0 00-2.12 0l-.88.879.97.97a.75.75 0 11-1.06 1.06l-5.16-5.159a1.5 1.5 0 00-2.12 0L3 16.061zm10.125-7.81a1.125 1.125 0 112.25 0 1.125 1.125 0 01-2.25 0z"
                    clip-rule="evenodd"
                  />
                </svg>
                <div class="flex text-sm leading-6 text-gray-600">
                  <label
                    for="file-upload"
                    class="relative cursor-pointer rounded-md bg-white font-semibold text-indigo-600 focus-within:outline-none focus-within:ring-2 focus-within:ring-indigo-600 focus-within:ring-offset-2 hover:text-indigo-500"
                  >
                    <span>Upload a file</span>
                    <input
                      id="file-upload"
                      name="file-upload"
                      type="file"
                      class="sr-only"
                      @input="previewThumbnail"
                    />
                  </label>
                  <p class="pl-1">or drag and drop</p>
                </div>
                <p class="text-xs leading-5 text-gray-600">
                  PNG, JPG, GIF up to 10MB
                </p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div as="template" v-show="isLoading">
      <div as="div" class="relative z-10">
        <div
          as="template"
          enter="ease-out duration-300"
          enter-from="opacity-0"
          enter-to="opacity-100"
          leave="ease-in duration-200"
          leave-from="opacity-100"
          leave-to="opacity-0"
        >
          <div
            class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
          ></div>
        </div>

        <div class="fixed inset-0 z-10 overflow-y-auto">
          <div
            class="flex min-h-full min-w-full items-center justify-center p-4 text-center"
          >
            <div
              as="template"
              enter="ease-out duration-300"
              enter-from="opacity-0 translate-y-4 "
              enter-to="opacity-100 translate-y-0 "
              leave="ease-in duration-200"
              leave-from="opacity-100 translate-y-0"
              leave-to="opacity-0 translate-y-4 "
            >
              <div
                class="relative flex flex-col items-center justify-center transform overflow-hidden rounded-lg bg-white text-center shadow-xl transition-all"
              >
                <div class="absolute right-0 top-0 -ml-8 flex pr-3 pt-2">
                  <button
                    type="button"
                    class="rounded-md text-gray-300 hover:text-black focus:outline-none focus:ring-2 focus:ring-black"
                    @click="clear"
                  >
                    <span class="sr-only">Close panel</span>
                    <XMarkIcon class="h-6 w-6" aria-hidden="true" />
                  </button>
                </div>

                <div class="px-4 min-w-full">
                  <div
                    class="mb-4 mt-2 text-xl font-semibold leading-6 text-gray-900"
                  >
                    Panel
                  </div>
                </div>
                <!-- IMG -->

                <div
                  class="relative overflow-hidden mr-5 ml-5 mb-6 rounded-[5px]"
                >
                  <canvas
                    class="bg-white object-cover object-center"
                    ref="canvasImg"
                  ></canvas>
                  <canvas
                    class="absolute top-0 bg-transparent object-cover object-center w-full h-full"
                    ref="canvasSample"
                  ></canvas>
                </div>

                <!-- input text editor -->
                <div class="mb-7 px-4 flex align-middle" v-if="isTextEvent">
                  <div>
                    <input
                      type="text"
                      name="username"
                      id="username"
                      autocomplete="..."
                      class="mr-4 w-32 rounded block ring-1 ring-inset ring-gray-300 bg-transparent py-1.5 pl-1 text-gray-900 placeholder:text-gray-400 sm:text-sm hover:bg-gray-50"
                      placeholder="Введите текст..."
                      v-model="inputTextImg"
                    />
                  </div>

                  <div class="flex items-center">
                    <button
                      type="button"
                      @click="handlerAddTextImg"
                      class="mr-2 rounded-md bg-indigo-600 px-3 py-1 text-sm font-semibold text-white shadow-sm hover:bg-indigo-400 ring-1 ring-black ring-opacity-5 focus:outline-none"
                    >
                      Save
                    </button>
                    <button
                      @click="handlerResetCanas"
                      type="button"
                      class="rounded-md bg-white-600 px-3 py-1 text-sm font-semibold text-gray-900 shadow-sm hover:bg-red-700 hover:text-white active:bg-red-600 active:text-white outline-none ring-1 ring-black ring-opacity-5 focus:outline-none"
                    >
                      Cancel
                    </button>
                  </div>
                </div>

                <!-- input Poligon editor -->

                <div
                  class="relative px-4 mb-6 text-left flex items-center justify-end"
                  v-show="isPolyhedronEvent"
                >
                  <div class="mr-2">
                    <button
                      @click="isSelectPolygons = !isSelectPolygons"
                      type="button"
                      class="inline-flex w-full justify-center gap-x-1.5 rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                      id="menu-button"
                    >
                      Коли-во полигонов
                      <svg
                        class="-mr-1 h-5 w-5 text-gray-400"
                        viewBox="0 0 20 20"
                        fill="currentColor"
                        aria-hidden="true"
                      >
                        <path
                          fill-rule="evenodd"
                          d="M5.23 7.21a.75.75 0 011.06.02L10 11.168l3.71-3.938a.75.75 0 111.08 1.04l-4.25 4.5a.75.75 0 01-1.08 0l-4.25-4.5a.75.75 0 01.02-1.06z"
                          clip-rule="evenodd"
                        />
                      </svg>
                    </button>
                  </div>
                  <div class="flex items-center">
                    <button
                      type="button"
                      @click="handlerAddPolyhedronImg"
                      class="mr-2 rounded-md bg-indigo-600 px-3 py-1 text-sm font-semibold text-white shadow-sm hover:bg-indigo-400 ring-1 ring-black ring-opacity-5 focus:outline-none"
                    >
                      Save
                    </button>
                    <button
                      @click="handlerResetCanas"
                      type="button"
                      class="rounded-md bg-white-600 px-3 py-1 text-sm font-semibold text-gray-900 shadow-sm hover:bg-red-700 hover:text-white active:bg-red-600 active:text-white outline-none ring-1 ring-black ring-opacity-5 focus:outline-none"
                    >
                      Cancel
                    </button>
                  </div>
                  <div
                    v-show="isSelectPolygons"
                    class="absolute flex items-center top-10 left-8 z-10 h-36 w-28 origin-top-right rounded-md bg-white shadow-lg ring-1 ring-black ring-opacity-5 focus:outline-none overflow-y-scroll"
                    role="menu"
                    aria-orientation="vertical"
                    aria-labelledby="menu-button"
                    tabindex="-1"
                  >
                    <div class="py-1 text-center" role="none">
                      <!-- Active: "bg-gray-100 text-gray-900", Not Active: "text-gray-700" -->
                      <a
                        v-for="value in polygonPointsSelectList"
                        :key="value"
                        @click="addPolygonPointsValue(value)"
                        href="#"
                        class="text-gray-700 block px-4 py-2 text-sm"
                        role="menuitem"
                        tabindex="-1"
                        id="menu-item-0"
                        >{{ value }}</a
                      >
                    </div>
                  </div>
                </div>

                <div v-show="isRectangleEvent" class="flex items-center mb-7">
                  <button
                    @click="handlerAddRectImg"
                    type="button"
                    class="mr-2 rounded-md bg-indigo-600 px-3 py-1 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500"
                  >
                    Save
                  </button>
                  <button
                    @click="handlerResetCanas"
                    type="button"
                    class="rounded-md bg-white-600 px-3 py-1 text-sm font-semibold text-gray-900 shadow-sm hover:bg-red-700 hover:text-white active:bg-red-600 active:text-white outline-none ring-1 ring-black ring-opacity-5 focus:outline-none"
                  >
                    Cancel
                  </button>
                </div>

                <!-- top btn  -->

                <div v-if="isEdit" class="relative flex-1 px-4 mb-8">
                  <div class="flex">
                    <span class="">
                      <button
                        @click="handlerTextButton"
                        type="button"
                        class="inline-flex items-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                      >
                        <PencilSquareIcon
                          class="-ml-0.5 mr-1.5 h-5 w-5 text-gray-400"
                          aria-hidden="true"
                        />
                        Text
                      </button>
                    </span>

                    <span class="ml-3">
                      <button
                        @click="handlerPolyhedronButton"
                        type="button"
                        class="inline-flex items-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                      >
                        <StarIcon
                          class="-ml-0.5 mr-1.5 h-5 w-5 text-gray-400"
                          aria-hidden="true"
                        />
                        Polyhedron
                      </button>
                    </span>

                    <span class="ml-3">
                      <button
                        @click="handlerRectangleButton"
                        type="button"
                        class="inline-flex items-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                      >
                        <StopIcon
                          class="-ml-0.5 mr-1.5 h-5 w-5 text-gray-400"
                          aria-hidden="true"
                        />
                        Box
                      </button>
                    </span>
                  </div>
                </div>

                <!-- bottom btn  -->

                <div class="relative flex-1 px-4 mb-8">
                  <div class="flex">
                    <span class="mr-3">
                      <button
                        type="button"
                        @click="handleAddEditor"
                        class="inline-flex items-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                      >
                        <PencilIcon
                          class="-ml-0.5 mr-1.5 h-5 w-5 text-gray-400"
                          aria-hidden="true"
                        />
                        Edit
                      </button>
                    </span>

                    <span class="mr-3">
                      <button
                        @click="handleSaveImage"
                        type="button"
                        class="inline-flex items-center rounded-md bg-indigo-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
                      >
                        <PhotoIcon
                          class="-ml-0.5 mr-1.5 h-5 w-5"
                          aria-hidden="true"
                        />
                        Publish
                      </button>
                    </span>

                    <span class="">
                      <button
                        @click="handlerResetImg"
                        type="button"
                        class="inline-flex items-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                      >
                        <XCircleIcon
                          class="-ml-0.5 mr-1.5 h-5 w-5 text-gray-400"
                          aria-hidden="true"
                        />
                        Reset
                      </button>
                    </span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue';
import {
  XMarkIcon,
  PhotoIcon,
  PencilSquareIcon,
  XCircleIcon,
} from '@heroicons/vue/24/outline';
import {
  LinkIcon,
  PencilIcon,
  StarIcon,
  StopIcon,
} from '@heroicons/vue/20/solid';

export default {
  name: 'App',

  components: {
    XMarkIcon,
    LinkIcon,
    PencilIcon,
    PhotoIcon,
    PencilSquareIcon,
    StarIcon,
    StopIcon,
    XCircleIcon,
  },

  data: () => {
    return {
      isLoading: false,
      isEdit: false,

      canvasImg: null,
      contextCanvasImg: null,
      canvasSample: null,
      contextCanvasSample: null,

      imageCanvas: null,

      isTextEvent: false,
      isTextAdd: false,
      inputTextImg: '',
      textX: 100,
      textY: 100,

      isPolyhedronEvent: false,
      isSelectPolygons: false,
      isPreviewPolyhedronFlag: true,
      polygonPointsValue: 3,
      polygonPointsSelectList: [1, 2, 3, 4, 5, 6, 7, 8],
      addPointsPreview: 0,
      polygonPoints: [],

      isRectangleEvent: false,
      isRectAdd: false,
      startWidhtRect: 100,
      startHeigthRect: 100,
      rectWidth: null,
      rectHeigth: null,
      rectX: null,
      rectY: null,

      clickX: null,
      clickY: null,
      mouseX: null,
      mouseY: null,
      isMousedown: false,

      maxWidth: 500,
      lineWidth: 4,
      fontSize: 16,
    };
  },

  mounted() {
    const dropContainer = this.$refs.dropContainer;

    dropContainer.addEventListener('drop', this.onDrop, true);
    dropContainer.addEventListener('dragenter', this.onDragEnter, true);
    dropContainer.addEventListener('dragover', this.onDragOver, true);

    this.canvasImg = this.$refs.canvasImg;
    this.contextCanvasImg = this.canvasImg.getContext('2d');

    this.canvasSample = this.$refs.canvasSample;
    this.contextCanvasSample = this.canvasSample.getContext('2d');

    // *** DESKTOP EVENT***

    this.canvasSample.addEventListener('mousedown', this.handleMousedownSample);
    this.canvasSample.addEventListener('mousemove', this.handleMousemoveSample);
    this.canvasSample.addEventListener(
      'mouseleave',
      this.handleMouseleaveSample
    );
    this.canvasSample.addEventListener('mouseup', this.handlerMouseupSample);

    // *** MOBILE EVENT ***

    this.canvasSample.addEventListener(
      'touchstart',
      this.handleMousedownSample
    );
    this.canvasSample.addEventListener('touchmove', this.handleMousemoveSample);
    this.canvasSample.addEventListener('touchend', this.handleMouseleaveSample);
    this.canvasSample.addEventListener('touchend', this.handlerMouseupSample);
  },

  computed: {},

  methods: {
    // *** DOWNOLAND and SET IMG ***

    previewThumbnail(event) {
      this.handleFiles(event.target.files);
    },

    handleFiles(files) {
      if (!files || !files[0]) {
        return;
      }

      if (!/^image\//.test(files[0].type)) {
        return;
      }

      this.isLoading = true;
      this.selectImageCanvas(files[0]);
    },

    selectImageCanvas(file) {
      let reader = new FileReader();

      reader.readAsDataURL(file);

      // Set new image load handler
      reader.onload = this.onImageLoad;
    },

    onImageLoad(e) {
      this.imageCanvas = new Image();

      this.imageCanvas.onload = () => {
        this.canvasImg.width = this.imageCanvas.width;
        this.canvasImg.height = this.imageCanvas.height;

        this.canvasSample.width = this.imageCanvas.width;
        this.canvasSample.height = this.imageCanvas.height;

        this.canvasImg.style.maxWidth = `${this.maxWidth}px`;
        this.contextCanvasImg.drawImage(this.imageCanvas, 0, 0);

        this.addStartTextPosition();
      };

      this.imageCanvas.src = e.target.result;
    },

    // *** DRAG AND DROP ***

    onDragEnter(e) {
      e.stopPropagation();
      e.preventDefault();
    },

    onDragOver(e) {
      e.stopPropagation();
      e.preventDefault();
    },

    onDrop(e) {
      e.stopPropagation();
      e.preventDefault();
      if (e.dataTransfer.files) {
        this.handleFiles(e.dataTransfer.files);
      }
    },

    // *** CANVAS MOUSE EVENT ***

    handleMousedownSample(event) {
      this.isMousedown = true;

      this.clickX = event.offsetX * this.em();
      this.clickY = event.offsetY * this.em();

      if (this.isTextEvent) {
        this.mousedownTextEvent();
      } else if (this.isPolyhedronEvent) {
        this.mousedownPolyhedronEvent();
      } else if (this.isRectangleEvent) {
        this.mousedownRectEvent();
      }
    },

    handlerMouseupSample() {
      this.isMousedown = false;
    },

    handleMouseleaveSample() {
      this.isMousedown = false;
    },

    handleMousemoveSample() {
      this.mouseX = event.offsetX * this.em();
      this.mouseY = event.offsetY * this.em();

      if (this.isTextEvent) {
        this.mousemoveTextEvent();
      } else if (this.isRectangleEvent) {
        this.mousemoveRectEvent();
      }
    },

    // *** SETTINGS CANVAS FIGURES ***

    settingsStyleTextPreview() {
      const fsEm = this.em() * this.fontSize;

      this.contextCanvasSample.font = `bold ${fsEm}px Verdana, sans-serif`;
      this.contextCanvasSample.textAlign = 'center';
      this.contextCanvasSample.textBaseline = 'middle';
      this.contextCanvasSample.fillStyle = 'rgba(255, 255, 255, 0.75)';
    },

    settingsStyleTextImg() {
      const fsEm = this.em() * this.fontSize;
      this.contextCanvasImg.font = `bold ${fsEm}px Verdana, sans-serif`;
      this.contextCanvasImg.textAlign = 'center';
      this.contextCanvasImg.textBaseline = 'middle';
      this.contextCanvasImg.fillStyle = '#fff';
    },

    settingsStylePoligonPreview() {
      this.contextCanvasSample.lineWidth = this.em() * this.lineWidth - 1;
      this.contextCanvasSample.strokeStyle = 'rgba(255, 255, 255, 0.75)';
      this.contextCanvasSample.setLineDash([this.em() * 5, this.em() * 10]);
      this.contextCanvasSample.lineCap = 'round';
    },

    settingsStylePoligonImg() {
      this.contextCanvasImg.lineWidth = this.em() * this.lineWidth;
      this.contextCanvasImg.strokeStyle = '#fff';
    },

    settingsStyleRectPreview() {
      this.contextCanvasSample.lineWidth = this.em() * this.lineWidth - 1;
      this.contextCanvasSample.strokeStyle = 'rgba(255, 255, 255, 0.75)';
      this.contextCanvasSample.setLineDash([this.em() * 5, this.em() * 10]);
    },

    settingsStyleRectImg() {
      this.contextCanvasImg.lineWidth = this.em() * this.lineWidth;
      this.contextCanvasImg.strokeStyle = '#fff';
    },

    // *** EVENTS CANVAS ***

    // *** TEXT EVENTS ***

    addStartTextPosition() {
      this.textX = this.canvasImg.width / 2;
      this.textY = this.canvasImg.height / 2;
    },

    mousedownTextEvent() {
      if (!this.isTextAdd) {
        this.previewFillText(this.clickX, this.clickY);
      }
    },

    mousemoveTextEvent() {
      if (
        this.isTextAdd &&
        this.isMousedown &&
        this.isPreviewTextCoordinates()
      ) {
        this.previewFillText(this.mouseX, this.mouseY);
      }
    },

    isPreviewTextCoordinates() {
      if (
        this.mouseX > this.textX - this.em() * 30 &&
        this.mouseX < this.textX + this.em() * 30 &&
        this.mouseY > this.textY - this.em() * 30 &&
        this.mouseY < this.textY + this.em() * 30
      ) {
        return true;
      } else {
        return false;
      }
    },

    previewFillText(x, y) {
      this.isTextAdd = true;
      this.textX = x;
      this.textY = y;

      this.resetCanvasSample();

      this.settingsStyleTextPreview();

      this.contextCanvasSample.fillText(this.inputTextImg, x, y);
    },

    addTextEventImg() {
      this.settingsStyleTextImg();

      this.contextCanvasImg.fillText(this.inputTextImg, this.textX, this.textY);
    },

    handlerAddTextImg() {
      this.addTextEventImg();
      this.handlerResetCanas();
    },

    // *** POLYHEDRON EVENTS ***

    mousedownPolyhedronEvent() {
      this.previewPolyhedronPoint();

      if (
        this.polygonPoints.length === this.polygonPointsValue &&
        this.isPreviewPolyhedronFlag
      ) {
        this.previewPolyhedronEvent();
      }
    },

    addPolygonPointsValue(value) {
      this.handlerResetCanas();
      this.polygonPointsValue = value;
      this.isSelectPolygons = false;
    },

    pushPolyhedronCoordinates() {
      this.polygonPoints.push({ x: this.mouseX, y: this.mouseY });
    },

    previewPolyhedronPoint() {
      if (this.polygonPointsValue > this.addPointsPreview) {
        this.handlerAddPolyhedronPoint();
      }

      if (this.polygonPoints.length < this.polygonPointsValue) {
        this.pushPolyhedronCoordinates();
      }
    },

    handlerAddPolyhedronPoint() {
      this.contextCanvasSample.beginPath();
      this.contextCanvasSample.arc(this.mouseX, this.mouseY, 0, 0, Math.PI * 2);

      this.settingsStylePoligonPreview();

      this.contextCanvasSample.stroke();

      this.addPointsPreview += 1;
    },

    previewPolyhedronEvent() {
      if (this.polygonPoints.length === this.polygonPointsValue) {
        this.isPreviewPolyhedronFlag = false;

        this.contextCanvasSample.beginPath();

        this.contextCanvasSample.moveTo(
          this.polygonPoints[0].x,
          this.polygonPoints[0].y
        );

        for (let i = 1; i < this.polygonPointsValue; i++) {
          this.contextCanvasSample.lineTo(
            this.polygonPoints[i].x,
            this.polygonPoints[i].y
          );
        }

        this.contextCanvasSample.closePath();
        this.settingsStylePoligonPreview();
        this.contextCanvasSample.stroke();
      }
    },

    handlerAddPolyhedronImg() {
      if (this.polygonPoints.length === this.polygonPointsValue) {
        this.contextCanvasImg.beginPath();

        this.contextCanvasImg.moveTo(
          this.polygonPoints[0].x,
          this.polygonPoints[0].y
        );

        for (let i = 1; i < this.polygonPointsValue; i++) {
          this.contextCanvasImg.lineTo(
            this.polygonPoints[i].x,
            this.polygonPoints[i].y
          );
        }

        this.contextCanvasImg.closePath();
        this.settingsStylePoligonImg();
        this.contextCanvasImg.stroke();
        this.handlerResetCanas();
      }
    },

    // ***  RECTENLE EVENT  ***

    findPolygonsRect() {
      if (
        this.mouseX < this.rectX + this.em() * 35 &&
        this.mouseX > this.rectX - this.em() * 15 &&
        this.mouseY < this.rectY + this.em() * 35 &&
        this.mouseY > this.rectY - this.em() * 15
      ) {
        this.handlerPolygonRectLT();
      } else if (
        this.mouseX < this.rectX + this.rectWidth + this.em() * 15 &&
        this.mouseX > this.rectX + this.rectWidth - this.em() * 35 &&
        this.mouseY < this.rectY + this.em() * 15 &&
        this.mouseY > this.rectY - this.em() * 35
      ) {
        this.handlerPolygonRectRT();
      } else if (
        this.mouseX < this.rectX + this.rectWidth + this.em() * 15 &&
        this.mouseX > this.rectX + this.rectWidth - this.em() * 35 &&
        this.mouseY < this.rectY + this.rectHeigth + this.em() * 15 &&
        this.mouseY > this.rectY + this.rectHeigth - this.em() * 35
      ) {
        this.handlerPolygonRectRB();
      } else if (
        this.mouseX < this.rectX + this.em() * 35 &&
        this.mouseX > this.rectX - this.em() * 15 &&
        this.mouseY < this.rectY + this.rectHeigth + this.em() * 35 &&
        this.mouseY > this.rectY + this.rectHeigth - this.em() * 15
      ) {
        this.handlerPolygonRectLB();
      }
    },

    isFindRect() {
      if (
        this.mouseX > this.rectX + this.em() * 15 &&
        this.mouseX < this.rectX + this.rectWidth - this.em() * 15 &&
        this.mouseY > this.rectY + this.em() * 15 &&
        this.mouseY < this.rectY + this.rectHeigth - this.em() * 15
      ) {
        return true;
      } else {
        return false;
      }
    },

    handlerPolygonRectLT() {
      this.rectWidth += this.rectX - this.mouseX;
      this.rectHeigth += this.rectY - this.mouseY;
      this.rectX = this.mouseX;
      this.rectY = this.mouseY;

      this.addRectPreview();
    },

    handlerPolygonRectRT() {
      this.rectWidth = this.mouseX - this.rectX;
      this.rectHeigth = this.rectY + this.rectHeigth - this.mouseY;
      this.rectY += this.mouseY - this.rectY;

      this.addRectPreview();
    },

    handlerPolygonRectRB() {
      this.rectWidth = this.mouseX - this.rectX;
      this.rectHeigth = this.mouseY - this.rectY;

      this.addRectPreview();
    },

    handlerPolygonRectLB() {
      this.rectWidth += this.rectX - this.mouseX;
      this.rectHeigth = this.mouseY - this.rectY;
      this.rectX = this.mouseX;

      this.addRectPreview();
    },

    mousemoveRectEvent() {
      if (this.isRectAdd && this.isFindRect() && this.isMousedown) {
        this.addСoordinatesRect();
        this.addRectPreview();
      } else if (this.isRectAdd && this.isMousedown) {
        this.findPolygonsRect();
      }
    },

    mousedownRectEvent() {
      if (!this.isRectAdd) {
        this.addStartСoordinatesRect();
        this.addRectPreview();
      }
    },

    addRectPreview() {
      this.handlerResetCanas();

      this.settingsStyleRectPreview();

      this.contextCanvasSample.beginPath();
      this.contextCanvasSample.rect(
        this.rectX,
        this.rectY,
        this.rectWidth,
        this.rectHeigth
      );
      this.contextCanvasSample.stroke();

      this.isRectAdd = true;
    },

    addStartСoordinatesRect() {
      this.rectWidth = (this.startWidhtRect / 2) * this.em();
      this.rectHeigth = (this.startHeigthRect / 2) * this.em();
      this.rectX = this.clickX - this.rectWidth / 2;
      this.rectY = this.clickY - this.rectHeigth / 2;
    },

    addСoordinatesRect() {
      this.rectWidth = this.rectWidth;
      this.rectHeigth = this.rectHeigth;
      this.rectX = this.mouseX - this.rectWidth / 2;
      this.rectY = this.mouseY - this.rectHeigth / 2;
    },

    handlerAddRectImg() {
      this.handlerResetCanas();

      this.settingsStyleRectImg();

      this.contextCanvasImg.beginPath();
      this.contextCanvasImg.rect(
        this.rectX,
        this.rectY,
        this.rectWidth,
        this.rectHeigth
      );
      this.contextCanvasImg.stroke();
    },

    // *** HANDLER EVENTS ***

    handleSaveImage() {
      const link = document.createElement('a');
      link.download = 'image-edit.jpg';
      link.href = this.canvasImg.toDataURL();
      link.click();
      link.remove();
    },

    handleAddEditor() {
      this.isEdit = !this.isEdit;
      this.isTextEvent = false;
      this.isPolyhedronEvent = false;
      this.isRectangleEvent = false;
    },

    handlerTextButton() {
      this.isTextEvent = !this.isTextEvent;
      this.isPolyhedronEvent = false;
      this.isRectangleEvent = false;
    },

    handlerPolyhedronButton() {
      this.isPolyhedronEvent = !this.isPolyhedronEvent;
      this.isTextEvent = false;
      this.isRectangleEvent = false;
    },

    handlerRectangleButton() {
      this.isRectangleEvent = !this.isRectangleEvent;
      this.isTextEvent = false;
      this.isPolyhedronEvent = false;
    },

    // *** CLEAR EVENT ***

    resetCanvasSample() {
      this.contextCanvasSample.clearRect(
        0,
        0,
        this.contextCanvasSample.canvas.width,
        this.contextCanvasSample.canvas.height
      );
    },

    handlerResetCanas() {
      this.resetCanvasSample();
      this.isTextAdd = false;
      this.inputTextImg = '';
      this.polygonPoints = [];
      this.addPointsPreview = 0;
      this.isRectAdd = false;
      this.isPreviewPolyhedronFlag = true;
    },

    handlerResetImg() {
      this.contextCanvasImg.drawImage(this.imageCanvas, 0, 0);
      this.handlerResetCanas();
    },

    clear() {
      // this.imageCanvas = null;
      // this.canvasImg.width = this.canvasImg.width;
      // this.canvasSample.width = this.canvasSample.width;
      this.isLoading = false;
      this.isTextEvent = false;
      this.isPolyhedronEvent = false;
      this.isRectangleEvent = false;
      location.reload();
    },

    // converter
    em() {
      return this.canvasImg.width > this.maxWidth
        ? this.canvasImg.width / this.maxWidth
        : 1;
    },
  },
  watch: {
    polygonPointsValue() {
      this.handlerResetCanas();
    },
    inputTextImg() {
      this.previewFillText(this.textX, this.textY);
    },
  },
};
</script>

<style scoped></style>
