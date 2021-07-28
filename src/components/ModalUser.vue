<template>
  <q-dialog ref='dialog' v-model='props.modalOpen' persistent>
    <q-card class='q-dialog-plugin'>
      <q-card-section>
        <div class="text-h6">{{props.title}}</div>
      </q-card-section>
      <q-card-section>

        <q-form ref='userForm' class='q-gutter-md'>
          <q-input
            filled
            v-model='props.user.firstname'
            label='Nombre'
            :rules="[ val => val && val.length > 0 || 'Campo requerido']"
          />
          <q-input
            filled
            v-model='props.user.fathers_lastname'
            label='Apellido Materno'
            :rules="[ val => val && val.length > 0 || 'Campo requerido']"
          />
          <q-input
            filled
            v-model='props.user.mothers_lastname'
            label='Apellido Paterno'
            :rules="[ val => val && val.length > 0 || 'Campo requerido']"
          />
          <q-input
            filled
            v-model='props.user.email'
            label='Correo electrónico'
            :rules="[ val => val && val.length > 0 || 'Campo requerido']"
          />
          <q-input
            filled
            v-model='props.user.dni'
            label='DNI'
            :rules="[ val => val && val.length > 0 || 'Campo requerido']"
          />
        </q-form>
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

export default defineComponent({
  name: 'ModalUser',
  props: {
    title: {
      type: String,
      required: true
    },
    user: {
      type: Object,
      required: true
    },
    modalOpen: {
      type: Boolean,
      required: true
    }
  },
  setup: function(props, { emit }) {
    const userForm = ref(null);

    function confirm() {
      userForm.value.validate().then(success => {
        if (success) {
          console.log('form OK');
          emit('onConfirm');
        } else {
          console.log('form con errores');
        }
      });
    }

    function cancel() {
      emit('onCancel');
    }

    return {
      props,
      confirm,
      cancel,
      userForm,
    };
  }
});

</script>
