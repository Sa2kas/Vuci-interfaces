<template>
  <div class="modal">
    <div class="modal-overlay" @click="close"></div>
    <div class="modal-container">
      <div class="modal-header">
        <h3 class="table-title">
          {{name}}
        </h3>
        <div class="modal-close">
          <div class="modal-close-button" @click="close"/>
        </div>
      </div>
      <div class="line"/>
      <div class="modal-body">
        <slot></slot>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  data () {
    return {
    }
  },
  methods: {
    close: function () {
      this.$emit('close')
    }
  },
  props: {
    index: {
      type: Number,
      required: false,
      default: 0
    },
    name: {
      type: String,
      required: false,
      default: ''
    }
  }
}
</script>
<style>
.modal {
  position: fixed;
  left: 50%;
  top: 15vh;
  z-index: 1;
  display: block;
  transition: all 1s ease-in;
}
.modal-overlay {
  cursor: auto;
  display: flex;
  position: fixed;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100%;
  background-color: rgba(0, 0, 0, 0.25);
  top: 0;
  left: 0;
  z-index: 1;
  transition: opacity .15s linear;
}
.modal-container {
  left: -50%;
  cursor: auto;
  width: 40vw;
  position: relative;
  border-radius: 7px;
  z-index: 2;
  background: #fff;
  min-height: 20vh;
  /* padding: 7px 15px 7px 15px; */
  padding: 7px 0;
  transition: scale .2s ease-in;
  display: block;
  min-width: 600px;
}
.modal-header {
  position: relative;
  height: 40px;
  margin-left: 10px;
  margin-top: 5px;
  display: block;
  padding: 0 15px;
  display: flex;
  justify-content: space-between;
}
.table-title {
  text-transform: uppercase;
}
.line {
  display: block;
  margin-bottom: 25px;
  width: 100%;
  height: 1px;
  background-color: #f1f1f1;
}
.modal-close {
  width: 20px;
  height: 20px;
  margin-left: auto;
  display: block;
}
.modal-close-button {
  position: absolute;
  right: 20px;
  top: 5px;
  width: 9px;
  height: 15px;
  display: block;
}
.modal-close-button::before {
  transition-duration: .3s;
  content: " ";
  background-color: #444;
  position: absolute;
  height: 17px;
  width: 2px;
  right: 9px;
  transform: rotate(-45deg);
}
.modal-close-button::after {
  transition-duration: .3s;
  content: " ";
  background-color: #444;
  position: absolute;
  height: 17px;
  width: 2px;
  right: 9px;
  transform: rotate(45deg);
}
.modal-close-button:hover::before,
.modal-close-button:hover::after {
  background-color: #0054a6;
  cursor: pointer;
}
.modal-body {
  /* height: calc(80vh - 116px); */
  overflow: auto;
  /* max-height: calc(80vh - 56px); */
  /* border: 1px solid #e4e4e4; */
  border-radius: unset;
  border-bottom-right-radius: 7px;
  border-bottom-left-radius: 7px;
  padding: 0 38px;
}
</style>
