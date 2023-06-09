<template>
  <div>
    <!-- description [start]-->
    <div class="description flex flex-col justify-center items-center gap-4">
      <!-- description.profile -->
      <img :src="user.profileImage" alt="user_profile" class="h-36 w-36 rounded-full border">

      <!-- description.nickname -->
      <h1 class="text-3xl italic">
        {{ user.nickname }}
      </h1>

      <h1 class="text-xs">
        {{ user.selfDesc || "이곳에는 자기소개가 들어갑니다." }}
      </h1>
    </div>
    <!-- description [end]-->



    <div class="main">
      <main>
        <div class="grid grid-cols-1 sm:grid-cols-1 gap-4 p-10">

          <router-link v-for="item in tasks" :key="item.taskId" :to="{ name: 'userTaskItems', params: { taskId: item.taskId, userId: this.$route.params.userId }}" :id="`card-${item.id}`">
            <div class="card bg-base-200 shadow-lg hover:shadow-2xl hover:cursor-pointer hover:bg-neutral-200 border rounded-lg">
              <div class="card-body items-center">
                <template v-if="item.itemCompletedCount / item.itemTotalCount === 1">
                  <div class="radial-progress text-green-600" :style="{ '--value': (item.itemCompletedCount/item.itemTotalCount)*100 }">{{ item.itemCompletedCount }} / {{ item.itemTotalCount }}</div>
                </template>
                <template v-if="item.itemCompletedCount / item.itemTotalCount >= 0.5 && item.itemCompletedCount / item.itemTotalCount < 1">
                  <div class="radial-progress text-yellow-600" :style="{ '--value': (item.itemCompletedCount/item.itemTotalCount)*100 }">{{ item.itemCompletedCount }} / {{ item.itemTotalCount }}</div>
                </template>
                <template v-if="item.itemCompletedCount / item.itemTotalCount < 0.5">
                  <div class="radial-progress text-red-600" :style="{ '--value': (item.itemCompletedCount/item.itemTotalCount)*100 }">{{ item.itemCompletedCount }} / {{ item.itemTotalCount }}</div>
                </template>
                <p class="italic my-5">{{ item.mainItem.itemTitle }}</p>
                <h4 class="text-xs text-gray-500 text-right">{{ getRelativeTime(item.taskCreatedDt) }}</h4>
              </div>
            </div>
          </router-link>

        </div>
      </main>
    </div>

  </div>
  <ButtonCreateTask/>
</template>


<script>
import {defineComponent} from 'vue'
import ButtonCreateTask from "@/components/ButtonCreateTask";
import taskService from "@/services/task/TaskService";
import {userService} from "@/services/user/UserService";
import dayjs from 'dayjs'
import relativeTime from 'dayjs/plugin/relativeTime'

dayjs.extend(relativeTime)

export default defineComponent({
  name: `Tasks`,
  components: {
    ButtonCreateTask
  },
  data() {
    return {
      user: 0,
      tasks: []
    }
  },
  mounted() {
    this.getUserProfile();
    this.fetchUserTasks();
  },
  methods: {
    // 모든 유저의 테스크를 가져옵니다.
    fetchUserTasks() {
      taskService.getAllUserTasks(this.$route.params.userId)
          .then(response => {
            this.tasks = response.data.data.tasks;
          }).catch(error => {
            console.log(JSON.stringify(error.data))
      })
    },

    // 유저 프로필 요청
    getUserProfile() {

      userService.getProfileInfo(this.$route.params.userId)
      .then(response => {
          this.user = response.data;
      }).catch(error => {
        console.log(`${JSON.stringify(error.data)}`)
      })
    },
    // 날짜계산
    getRelativeTime(date) {
      return dayjs(date).fromNow()
    },
  }
})
</script>
