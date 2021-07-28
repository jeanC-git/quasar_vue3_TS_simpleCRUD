<template>
  <q-page class='row items-center justify-evenly'>
    <div class='q-pa-md'>
      <q-table
        title='Users'
        :rows='rows'
        :columns='columns'
        row-key='name'
      />
    </div>
  </q-page>
  <ModalUser
    :modal-open='modalUser'
    :user='userTemp'
  />
</template>

<script lang='ts'>
import { defineComponent, ref, onMounted } from 'vue';
import { User } from 'src/models/User';
import ModalUser from '../components/ModalUser.vue';

export default defineComponent({
  name: 'PageIndex',
  components: {ModalUser},
  setup() {
    onMounted(() => {
      getData('');
    });
    let modalUser = ref(false)
    let userTemp = ref<User>({
      id: 0,
      first_name: '',
      fathers_lastname: '',
      mothers_lastname: '',
      dni: '',
      email: '',
    })
    const columns = [
      { name: 'id', label: 'ID', field: 'id' },
      { name: 'dni', label: 'DNI', field: 'dni' },
      { name: 'first_name', required: true, label: 'Nombre', align: 'left', field: 'first_name', sortable: true },
      { name: 'fathers_lastname', align: 'left', label: 'Apellido Paterno', field: 'fathers_lastname', sortable: true },
      { name: 'mothers_lastname', align: 'left', label: 'Apellido Paterno', field: 'mothers_lastname', sortable: true },
      { name: 'email', label: 'Correo Electrónico', field: 'email', sortable: true },
      { name: 'actions', label: 'Acciones', field: 'actions', sortable: true, align: 'center' }
    ];
    const rows = ref<User[]>([])
    function getData(endpoint: string, page = 1){
      console.log('asd');
    }
    return {
      ModalUser,
      modalUser,
      userTemp,
      columns,
      rows,
    };
  }
});
</script>
