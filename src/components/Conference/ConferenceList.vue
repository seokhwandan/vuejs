<template>
  <div class="container">
    <div class="d-flex justify-content-between">
      <el-select type="text" v-model="state.category" @change="categoryChange()">
        <el-option label="전체보기" value=""></el-option>
        <el-option label="일반" value="normal"></el-option>
        <el-option label="가이드" value="guide"></el-option>
      </el-select>
      <div v-if="state.token">
        <el-button @click="clickConferenceCreate">방 생성</el-button>
      </div>
    </div>
    <div class="row">
      <div class="col-4 g-4" v-for="conference in state.conferencePageList" :key="conference">
        <div class="d-flex justify-content-center">
          <div class="block">
            <img class="conference_thumbnail" src="../../assets/selfie1.jpg" @click="clickConference(conference.conferenceNo)">
            <div class="mt-2 d-flex align-items-center">
              <img class="profile" :src="state.circleUrl">
              <div>
                <span class="mt-2 mx-2">{{ conference.title }}</span><br>
                <span class="owner mx-2" @click="clickProfile">{{ conference.owner }}</span>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
  <div class="pagination">
    <el-pagination
      :page-size="9"
      layout="prev, pager, next"
      :total="state.conferenceCount"
      @current-change="pageChange">
    </el-pagination>
  </div>
</template>

<script>
import { onMounted, reactive, computed } from 'vue'
import { useRouter } from 'vue-router'
import { useStore } from 'vuex'

export default ({
  name: 'ConferenceList',
  setup(props, { emit }) {
    const router = useRouter()
    const store = useStore()

    const state = reactive ({
      circleUrl: require("@/assets/selfie.jpg"),
      token: computed(() => store.getters['getToken']),
      normal: computed(() => state.conferenceList.filter(function (conference) {
        return conference.conferenceCategory === 'normal'
      })),
      guide: computed(() => state.conferenceList.filter(function (conference) {
        return conference.conferenceCategory === 'guide'
      })),
      category: '',
      conferenceList: [],
      conferencePageList: [],
      conferenceCount: 0,
      conferenceCategoryList: [],
    })


    const clickProfile = () => {
      router.push({ name: 'Profile'})  
    }
    const clickConferenceCreate = () => {
      emit('openConferenceCreateDialog')
    }
    const clickConference = (conferenceNo) => {
      store.dispatch('conferenceDetail', conferenceNo)
        .then(() => {
          emit('openConferenceDialog')
        })
    }
    const categoryChange = () => {
      console.log(state.category)
      if (state.category === 'normal') {
        state.conferencePageList = state.normal.slice(0, 9)
        state.conferenceCount = state.normal.length
        console.log(state.category)
      }
      else if (state.category === 'guide') {
        state.conferencePageList = state.guide.slice(0, 9)
        state.conferenceCount = state.guide.length
        console.log(state.category)
      } else {
        state.conferencePageList = state.conferenceList.slice(0, 9)
        state.conferenceCount = state.conferenceList.length
        console.log(state.category)
      }
    }
    const pageChange = (val) => {
      const start = (val - 1) * 9
        if (state.category === 'normal') {
          state.conferencePageList = state.normal.slice(start, start + 9)
        }
        else if (state.category === 'guide') {
          state.conferencePageList = state.guide.slice(start, start + 9)
        } else {
          state.conferencePageList = state.conferenceList.slice(start, start + 9)
        }
    } 
    onMounted (() => {
      store.dispatch('conferenceList')
        .then(({ data }) => {
          state.conferenceList = data.content
          state.conferencePageList = data.content.slice(0, 9)
          state.conferenceCount = data.totalElements
        })
    })

    return { state, clickProfile, clickConferenceCreate, clickConference, pageChange, categoryChange }
  },
})
</script>

<style>
img:hover {
  cursor: pointer;
}
.conference_thumbnail {
  border-radius: 20px;
  width: 300px;
  height: 200px;
}
.profile {
  border-radius: 100%;
  width: 50px;
  height: 50px;
}
.owner:hover {
  cursor: pointer;
  color: black;
  font-weight: bold;
}
.pagination {
  position: fixed;
  bottom: 5rem;
  left: 45%;
  right: 55%;
}
</style>