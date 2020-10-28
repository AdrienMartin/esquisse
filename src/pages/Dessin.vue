<template>
  <div class="dessin">
    <div class="editor-container">
      <div class="editor">
        <div class="current-color" :style="{backgroundColor: color}"></div>
        <b-button variant="dark" @click="clear()">Effacer</b-button>
        <b-button variant="dark" @click="undo()">Annuler</b-button>
        <b-button variant="dark" @click="redo()">Refaire</b-button>
      </div>
      <Editor :canvasWidth="500" :canvasHeight="500" ref="editor" class="borderDessin"/>
    </div>
    <div class="colors">
      <div class="color-container red" @click="changeColor('#e40000')"></div>
      <div class="color-container yellow" @click="changeColor('#e8eb34')"></div>
      <div class="color-container purple" @click="changeColor('#a834eb')"></div>
      <div class="color-container green" @click="changeColor('#65c31a')"></div>
      <div class="color-container lightblue" @click="changeColor('#34b7eb')"></div>
      <div class="color-container pink" @click="changeColor('#eb34df')"></div>
      <div class="color-container blue" @click="changeColor('#1a10ad')"></div>
      <div class="color-container black" @click="changeColor('#000000')"></div>
    </div>
  </div>
</template>

<script>
import Editor from "vue-image-markup";

export default {
  components: {
    Editor,
  },
  data() {
    return {
       color: "black"
    };
  },
  methods: {
    clear() {
      this.$refs.editor.clear();
    },
    undo() {
      this.$refs.editor.undo();
      this.$refs.editor.set("freeDrawing");
    },
    redo() {
      this.$refs.editor.redo();
      this.$refs.editor.set("freeDrawing");
    },
    changeColor(colorHex) {
      this.color = colorHex;
      this.$refs.editor.changeColor(colorHex);
    },
    saveImage() {
      let image = this.$refs.editor.saveImage();
      this.$socket.emit('sendDrawOrWord', image);
    }
  },
  mounted() {
    this.$refs.editor.set("freeDrawing");
  }
};
</script>

<style>
.borderDessin {
  margin-top : 10px;
  border : 1px solid rgba(0,0,0,.13);
}
.current-color {
  border-radius: 5px;
  min-width: 28px;
  min-height: 28px;
}

.dessin {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 50px;
  -webkit-box-pack: center;
  -ms-flex-pack: center;
  justify-content: center;
}

.dessin,
.editor {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
}
.editor {
  -webkit-box-pack: justify;
  -ms-flex-pack: justify;
  justify-content: space-between;
}

.editor-container {
  -webkit-box-orient: vertical;
  -webkit-box-direction: normal;
  -ms-flex-direction: column;
  flex-direction: column;
}
.editor-container {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
}

.colors {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  -webkit-box-orient: vertical;
  -webkit-box-direction: normal;
  -ms-flex-direction: column;
  flex-direction: column;
  margin: 40px 25px 0 25px;
}
.color-container {
  border-radius: 50%;
  width: 30px;
  height: 30px;
  margin: 5px 0;
}

.red {
  background-color: #e40000;
}
.yellow {
  background-color: #e8eb34;
}
.purple {
  background-color: #a834eb;
}
.green {
  background-color: #65c31a;
}
.lightblue {
  background-color: #34b7eb;
}
.pink {
  background-color: #eb34df;
}
.blue {
  background-color: #1a10ad;
}
.black {
  background-color: #000;
}
</style>
