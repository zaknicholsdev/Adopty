<template>
  <div class="delete-modal pt-5" v-show="value">
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header justify-content-center">
          <h5 class="modal-title">Are you sure?</h5>
        </div>
        <div class="modal-body text-center">
          <p>This action cannot be undone.</p>
        </div>
        <div class="modal-footer">
          <button @click="deleteItem" type="button" class="btn btn-danger">Delete</button>
          <button
            @click.prevent="close"
            type="button"
            class="btn btn-secondary"
            data-dismiss="modal"
          >Close</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Modal",
  props: {
    value: {
      required: true
    }
  },
  methods: {
    close() {
      this.$emit("input", !this.value);
    },
    deleteItem() {
      this.$emit('deleteItem')
    }
  }
};
</script>


<style scoped>
.delete-modal {
  background-color: rgba(0, 0, 0, 0.7);
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 1050;
  overflow: hidden;
  outline: 0;
}
</style>