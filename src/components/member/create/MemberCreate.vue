<template>
  <div class="wrap_welcome">
    <h1 class="tit_comm">
      <router-link :to="{ name: 'Main' }" class="link_comm">덴버월드</router-link>
    </h1>

    <h2 class="screen_out">본문 영역</h2>
    <div class="inner_welcome">
      <strong class="tit_welcome">얼마 안 남았어요!</strong>
      <p class="desc_welcome">나머지 정보를 입력해주세요!</p>

      <create :isAuthorized="isAuthorized" @parentSubmit="onSubmit" />
    </div>
  </div>
  <!-- // wrap_welcome -->
</template>

<script>
import { mapGetters } from 'vuex'
import Create from '@/components/member/create/Create'
import api from '@/api'

export default {
  name: 'MemberCreate',
  components: { Create },
  computed: {
    ...mapGetters(['isAuthorized'])
  },
  methods: {
    onSubmit(payload) {
      api
        .post('/api/register', payload)
        .then((response) => {
          alert('회원가입에 성공했어요!')

          // console.log('[MemberCreate.vue] 회원가입에 성공했어요!')
          // console.log('[MemberCreate.vue] response: ', response)

          this.$router.push({ name: 'MemberLogin' })
        })
        .catch((error) => {
          alert('회원가입에 실패했어요.. ㅠㅜ')

          // console.log('[MemberCreate.vue] 회원가입에 실패했어요.. ㅠㅜ')
          // console.log('[MemberCreate.vue] error: ', error)
          // console.log('[MemberCreate.vue] error.message: ', error.message)
          // console.log('[MemberCreate.vue] error.response: ', error.response)
          // console.log('[MemberCreate.vue] error.response.data: ', error.response.data)
          // console.log('[MemberCreate.vue] error.response.data.message: ', error.response.data.message)
        })
    }
  }
}
</script>

<style scoped></style>
