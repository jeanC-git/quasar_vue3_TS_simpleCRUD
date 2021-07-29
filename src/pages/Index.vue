<template>
  <q-page class='row items-center justify-evenly'>
    <div class='q-pa-md'>
      <q-table
        :rows='rows'
        :loading='loading'
        :columns='columns'
        :pagination='pagination'
        title='Users'
        row-key='name'
        rows-per-page-label='Resultados por página'
        no-data-label='No hay datos que mostrar.'
        hide-pagination
      >
        <template v-slot:top>
          <q-btn class='q-ml-sm' color='primary' label='Refresh data' @click='refreshData' />
          <q-btn class='q-ml-sm' color='primary' label='Crear usuario' @click='createUser' />

        </template>
        <template v-slot:body-cell-actions='cell'>
          <q-td>
            <q-btn flat round class='q-ml-sm' color='primary' icon='mdi-pencil' @click='editUser(cell.row)' />
            <q-btn flat round class='q-ml-sm' color='primary' icon='mdi-delete' @click='deleteUser(cell.row)' />
          </q-td>
        </template>
      </q-table>
      <div class='row justify-center q-mt-md'>
        <q-pagination
          v-model='currentPage'
          color='grey-8'
          :max='pagination.maxPage'
        />
      </div>
    </div>
  </q-page>
  <ModalUser
    ref='refModalUser'
    :title='modalUserTitle'
    :modal-open='modalUser'
    :user='userTemp'
    @onConfirm='confirmModalUser'
    @onCancel='cancelModalUser'

  />
</template>

<script lang='ts'>
import { defineComponent, ref, onMounted, reactive, watch } from 'vue';
import { User } from 'src/models/User';
import ModalUser from '../components/ModalUser.vue';
import { api } from 'boot/axios';
import { useQuasar } from 'quasar';

export default defineComponent({
  name: 'PageIndex',
  components: { ModalUser },
  setup() {
    onMounted(() => {
      getData('user/get-all');
    });
    let modalUser = ref(false);
    let userTemp = ref({
      id: 0,
      first_name: '',
      fathers_lastname: '',
      mothers_lastname: '',
      dni: '',
      email: ''
    });
    let loading = ref(false);
    let pagination = reactive({
      rowsPerPage: 2,
      maxPage: 1
    });
    let currentPage = ref(1);
    let modalUserTitle = ref('Crear Usuario');
    let rows = ref([]);
    const columns = [
      { name: 'id', label: 'ID', field: 'id' },
      { name: 'dni', label: 'DNI', field: 'dni', align: 'left' },
      { name: 'firstname', required: true, label: 'Nombre', align: 'left', field: 'firstname', sortable: true },
      {
        name: 'fathers_lastname',
        align: 'left',
        label: 'Apellido Paterno',
        field: 'fathers_lastname',
        sortable: true
      },
      {
        name: 'mothers_lastname',
        align: 'left',
        label: 'Apellido Paterno',
        field: 'mothers_lastname',
        sortable: true
      },
      { name: 'email', label: 'Correo Electrónico', field: 'email', align: 'left', sortable: true },
      { name: 'actions', label: 'Acciones', field: 'actions', sortable: true, align: 'center' }
    ];
    const refModalUser = ref(null);

    const $q = useQuasar();

    watch(currentPage, (next) => {
      getData('user/get-all', Number(next));
    });

    function refreshData() {
      refModalUser.value.consoleLog();
      getData('user/get-all');
    }

    function getData(endpoint: string, page = 1) {
      loading.value = true;
      const url = `${endpoint}?page=${page}`;
      api.get(url)
        .then(res => {
          let data = res.data.data.data;
          pagination.maxPage = res.data.data.last_page;
          rows.value = data;
          setTimeout(() => {
            loading.value = false;
          }, 1000);
        })
        .catch(err => {
          console.log(err);
        });
    }

    function createUser() {
      modalUserTitle.value = 'Crear Usuario';
      modalUser.value = true;
    }

    async function editUser(user: User) {
      modalUserTitle.value = 'Editar Usuario';
      await api.get(`user/get-user/${user.id}`)
        .then(res => {
          userTemp.value = res.data.user;
        })
        .catch(err => {
          console.log(err);
        });
      modalUser.value = true;
    }

    function deleteUser(user: User) {
      $q.dialog({
        title: 'Eliminar Usuario',
        message: '¿Está seguro de eliminar este usuario?',
        cancel: true,
        persistent: true
      }).onOk(async () => {
        await api.delete(`user/delete-user/${user.id}`)
          .then(() => {
            getData('user/get-all');
          })
          .catch(err => {
            console.log(err);
          });
      });
    }

    async function confirmModalUser() {
      modalUser.value = false;
      const data = userTemp.value;
      if (data.id === 0) {
        await api.post('user/create-user', data)
          .then((res) => {
            // console.log(res.data);
          })
          .catch(err => {
            console.log(err);
          });
      } else {
        await api.put('user/edit-user', data)
          .then((res) => {
            // console.log(res.data);
          })
          .catch(err => {
            console.log(err);
          });
      }
      currentPage.value = 1;
      getData('user/get-all');
    }

    function cancelModalUser() {
      modalUser.value = false;
      resetUserTemp();
    }

    function resetUserTemp() {
      userTemp.value = {
        id: 0,
        first_name: '',
        fathers_lastname: '',
        mothers_lastname: '',
        dni: '',
        email: ''
      };
    }

    return {
      getData,
      createUser,
      editUser,
      deleteUser,
      confirmModalUser,
      cancelModalUser,
      refreshData,
      refModalUser,
      ModalUser,
      modalUser,
      userTemp,
      loading,
      columns,
      rows,
      pagination,
      currentPage,
      modalUserTitle
    };
  }
});
</script>
