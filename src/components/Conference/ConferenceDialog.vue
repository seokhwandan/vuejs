<template>
<el-dialog custom-class="conferenceDialog" title="방 입장" v-model="state.dialogVisible" @close="handleClose">
  <div class="container">
    <div class="d-flex align-items-end">
      <h2 class="mb-0 me-auto">{{ state.title }}</h2>
      <span>{{ state.category }}</span>
    </div>
    <hr class="article-line mb-3">
    <div class="d-flex align-items-center">
      <img class="dialog-profile me-2" src="../../assets/selfie1.jpg">
      <span>{{ state.owner }}</span>
    </div>
    <hr class="article-line mt-3 mb-3">
    <span>{{ state.description }}</span>
    <span v-if="state.password">
      <hr>
      <el-form :model="state" :rules="state.rules" ref="ConferenceDetailForm" :label-position="state.align">
        <el-form-item prop="inputPassword" label="비밀번호" :label-width="state.formLabelWidth">
          <el-input v-model="state.inputPassword" autocomplete="off" show-password></el-input>
        </el-form-item>
      </el-form>
    </span>
  </div>
  <template #footer>
    <span class="dialog-footer">
      <el-button type="primary" @click="clickConferenceDialog()">입장하기</el-button>
    </span>
  </template>
</el-dialog>
</template>

<script>
import { reactive, computed, ref } from 'vue'
import { useStore } from 'vuex'
import { useRouter } from 'vue-router'
import { ElMessage } from 'element-plus'

export default {
  name: 'ConferenceDialog',
  props: {
    open: {
      type: Boolean,
      default: false,
    }
  },
  setup(props, { emit }) {
    const store = useStore()
    const router = useRouter()
    const conferenceDialogForm = ref(null)

    const state = reactive({
      owner: computed(() => store.getters['getUsername']),
      title: computed(() => store.getters['getConferencetitle']),
      description: computed(() => store.getters['getConferencedescription']),
      category: computed(() => store.getters['getConferencecategory']),
      password: computed(() => store.getters['getConferencepassword']),
      inputPassword: '',
      limit: computed(() => store.getters['getConferencelimit']),
      conferenceNo: computed(() => store.getters['getConferenceno']),
      onPassword: false,
      align: 'left',
      rules: {
        password: [
          { required: true, message: 'Please input Password', trigger: 'blur' }
        ],
      },
      dialogVisible: computed(() => props.open),
      formLabelWidth: '120px',
    })

    const clickConferenceDialog = () => {
      if (state.password == state.inputPassword) {
        router.push({ name: 'Conference', params: { conferenceId: state.conferenceNo}})
        emit('closeConferenceDialog')
      } else {
        ElMessage.error('비밀번호가 틀렸습니다.')
      }
    }

    const handleClose = () => {
      state.inputPassword = ''
      emit('closeConferenceDialog')
    }

    return { conferenceDialogForm, state, clickConferenceDialog, handleClose }
  },
}
</script>

<style>
.conferenceDialog {
  width: 400px !important;
}
.dialog-profile {
  border-radius: 100%;
  width: 20px;
  height: 20px;
}
</style>