<template>
  <q-dialog ref='dialog'>
    <q-card class='q-dialog-plugin'>
      <q-card-section>
        <h1>FormUser</h1>
      </q-card-section>
      <q-card-actions align='right'>
        <q-btn color='primary' label='OK' @click='confirm' />
        <q-btn color='primary' label='Cancel' @click='cancel' />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script>
import { defineComponent, ref } from 'vue';
import { User } from 'src/models/User';

export default defineComponent({
  name: 'ModalUser',
  props: {
    user: {
      type: Object,
      required:true,
    },
    modalOpen: {
      type: Boolean,
      required: true
    }
  },
  setup({user, modalOpen}, {emit}) {

    function confirm() {
      emit('onConfirm');
    }

    function cancel() {
      emit('onCancel')
    }

    function close() {
      emit('onClose')
    }

    return {
      confirm,
      cancel,
      close
    };
  }
});

</script>
