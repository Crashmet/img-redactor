<template>
  <div>
    <div class="space-y-12">
      <div ref="dropContainer" class="border-b border-gray-900/10 pb-12">
        <h2 class="text-base font-semibold leading-7 text-gray-900">
          Редактор изображений
        </h2>
        <p class="mt-1 text-sm leading-6 text-gray-600">
          Добавляйте надписи или фигуры
        </p>

        <div class="mt-10 grid grid-cols-1 gap-x-6 gap-y-8 sm:grid-cols-6">
          <div class="col-span-full">
            <label
              for="cover-photo"
              class="block text-sm font-medium leading-6 text-gray-900"
              >Photo</label
            >
            <div
              class="mt-2 flex justify-center rounded-lg border border-dashed border-gray-900/25 px-6 py-10"
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
                <div class="mt-4 flex text-sm leading-6 text-gray-600">
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
          />
        </div>

        <div class="fixed inset-0 z-10 overflow-y-auto">
          <div
            class="flex min-h-full min-w-full items-center justify-center p-4 text-center sm:items-center sm:p-0"
          >
            <div
              as="template"
              enter="ease-out duration-300"
              enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
              enter-to="opacity-100 translate-y-0 sm:scale-100"
              leave="ease-in duration-200"
              leave-from="opacity-100 translate-y-0 sm:scale-100"
              leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
            >
              <div
                class="relative flex flex-col items-center justify-center transform overflow-hidden rounded-lg bg-white text-center shadow-xl transition-all sm:my-8"
              >
                <div
                  class="absolute right-0 top-0 -ml-8 flex pr-2 pt-4 sm:-ml-10 sm:pr-4"
                >
                  <button
                    type="button"
                    class="rounded-md text-gray-300 hover:text-black focus:outline-none focus:ring-2 focus:ring-black"
                    @click="clear"
                  >
                    <span class="sr-only">Close panel</span>
                    <XMarkIcon class="h-6 w-6" aria-hidden="true" />
                  </button>
                </div>

                <div class="px-4 sm:px-6 min-w-full">
                  <div
                    class="mb-4 mt-4 text-xl font-semibold leading-6 text-gray-900"
                  >
                    Panel IMG
                  </div>
                </div>
                <!-- IMG -->

                <div
                  class="relative overflow-hidden mr-5 ml-5 mb-4 rounded-[5px]"
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

                <div class="mb-6" v-if="isTextEvent">
                  <label
                    for="username"
                    class="block text-base font-medium leading-6 text-gray-900 mb-1"
                    >Введите текст</label
                  >
                  <input
                    type="text"
                    name="username"
                    id="username"
                    autocomplete="..."
                    class="w-full rounded block ring-1 ring-inset ring-gray-300 bg-transparent py-1.5 pl-1 text-gray-900 placeholder:text-gray-400 sm:text-sm sm:leading-6 hover:bg-gray-50"
                    placeholder="Enter text"
                    v-model="inputTextImg"
                  />
                </div>

                <!-- input Poligon editor -->

                <div class="mb-6" v-if="isPolyhedronEvent">
                  <label
                    for="username"
                    class="block text-base font-medium leading-6 text-gray-900 mb-1"
                    >Введите количество полигонов</label
                  >
                  <input
                    type="text"
                    name="username"
                    id="username"
                    autocomplete="..."
                    class="w-full rounded block ring-1 ring-inset ring-gray-300 bg-transparent py-1.5 pl-1 text-gray-900 placeholder:text-gray-400 sm:text-sm sm:leading-6 hover:bg-gray-50"
                    placeholder="Enter polygons value"
                    v-model="polygonPointsValue"
                  />
                </div>
                <!-- top btn  -->

                <div v-if="isEdit" class="relative flex-1 px-4 sm:px-6 mb-8">
                  <div class="mt-5 flex lg:ml-4 lg:mt-0">
                    <span class="hidden sm:block">
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

                    <span class="ml-3 hidden sm:block">
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

                    <span class="ml-3 hidden sm:block">
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

                <div class="relative flex-1 px-4 sm:px-6 mb-8">
                  <div class="mt-5 flex lg:ml-4 lg:mt-0">
                    <span class="hidden sm:block">
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

                    <span class="ml-3 hidden sm:block">
                      <button
                        @click="handlerResetCanas"
                        type="button"
                        class="inline-flex items-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50"
                      >
                        <LinkIcon
                          class="-ml-0.5 mr-1.5 h-5 w-5 text-gray-400"
                          aria-hidden="true"
                        />
                        Reset
                      </button>
                    </span>

                    <span class="sm:ml-3">
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
      inputTextImg: '',

      isPolyhedronEvent: false,
      polygonPointsValue: '3',
      polygonPoints: [],

      isRectangleEvent: false,

      clickX: null,
      clickY: null,
      mouseX: null,
      mouseY: null,

      maxWidth: 500,

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

    this.canvasSample.addEventListener('mousedown', this.handleMousedownSample);
    this.canvasSample.addEventListener('mousemove', this.handleMousemoveSample);
    document.addEventListener('mouseup', this.handlerMouseup);
  },

  computed: {},

  methods: {
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

    // загружаем изображение
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
      };

      this.imageCanvas.src = e.target.result;
    },

    // on Drag

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

    // canvas
    handleMousedownSample(event) {
      this.clickX = event.offsetX * this.em();
      this.clickY = event.offsetY * this.em();

      if (this.isTextEvent) {
        this.fillTextEvent();
      } else if (this.isPolyhedronEvent) {
        this.previewPolyhedronPoint();
      } else if (this.isRectangleEvent) {
        this.rectEvent();
      }
    },

    handleMousemoveSample() {
      this.mouseX = event.offsetX * this.em();
      this.mouseY = event.offsetY * this.em();

      if (this.isTextEvent) {
        this.previewFillText();
      } else if (this.isPolyhedronEvent) {
        this.previewPolyhedronEvent();
      }
    },

    // events Canvas

    fillTextEvent() {
      const fsEm = this.em() * this.fontSize;

      this.contextCanvasImg.font = `bold ${fsEm}px Verdana, sans-serif`;
      this.contextCanvasImg.fillStyle = '#fff';

      this.contextCanvasImg.fillText(
        this.inputTextImg,
        this.clickX,
        this.clickY
      );
    },

    // rectEvent() {
    //   this.contextCanvasImg.strokeRect(this.clickX, this.clickY, 50, 50);
    // },

    previewFillText() {
      const fsEm = this.em() * this.fontSize;

      this.contextCanvasSample.font = `bold ${fsEm}px Verdana, sans-serif`;
      this.contextCanvasSample.fillStyle = '#000';

      this.resetCanvasSample();

      this.contextCanvasSample.fillText(
        this.inputTextImg,
        this.mouseX,
        this.mouseY
      );
    },

    previewPolyhedronPoint() {
      this.polygonPoints.push({ x: this.mouseX, y: this.mouseY });

      this.contextCanvasSample.beginPath();
      this.contextCanvasSample.moveTo(this.mouseX, this.mouseY);

      this.contextCanvasSample.lineTo(this.mouseX, this.mouseY);

      this.contextCanvasSample.strokeStyle = 'green';
      this.contextCanvasSample.lineWidth = this.em() * 4;
      this.contextCanvasSample.lineCap = 'round';

      this.contextCanvasSample.stroke();
    },

    // previewPolyhedronEvent() {
    //   this.contextCanvasSample.strokeStyle = 'green';
    //   this.contextCanvasSample.lineWidth = this.em() * 4;

    //   this.contextCanvasSample.beginPath();

    //   // if (this.polygonPoints.length === 1) {
    //   this.contextCanvasSample.moveTo(
    //     this.polygonPoints[0].x,
    //     this.polygonPoints[0].y
    //   );
    //   // } else {
    //   for (let i = 1; i < this.polygonPointsValue; i++) {
    //     this.contextCanvasSample.lineTo(
    //       this.polygonPoints[i].x,
    //       this.polygonPoints[i].y
    //     );
    //   }
    //   // }
    //   this.contextCanvasSample.closePath();
    //   this.contextCanvasSample.stroke();
    // },

    resetCanvasSample() {
      this.contextCanvasSample.clearRect(
        0,
        0,
        this.contextCanvasSample.canvas.width,
        this.contextCanvasSample.canvas.height
      );
    },

    // handler events
    handlerMouseup() {
      //
    },

    handlerResetCanas() {
      this.resetCanvasSample();
      this.polygonPoints = [];
    },

    handleAddEditor() {
      this.isEdit = !this.isEdit;
      this.isTextEvent = false;
      this.isPolyhedronEvent = false;
      this.isRectangleEvent = false;
    },

    handleSaveImage() {
      const link = document.createElement('a');
      link.download = 'image-edit.jpg';
      link.href = this.canvasImg.toDataURL();
      link.click();
      link.remove();
    },

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
  },
};
</script>

<style scoped></style>
