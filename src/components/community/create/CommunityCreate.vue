<template>
  <div class="container">
    <h2 class="screen_out">본문 영역</h2>

    <div class="contents create">
      <create :category2="service" @parentSubmit="onSubmit" />
    </div>
    <!-- // contents -->
  </div>
  <!-- // container -->
</template>

<script>
import Create from '@/components/community/create/Create'
import api from '@/api'

export default {
  name: 'CommunityCreate',
  props: {
    service: {
      type: String,
      required: true
    }
  },
  components: { Create },
  methods: {
    onSubmit(payload) {
      api
        .post(`/api/board/${payload.get('category')}/write`, payload)
        .then((response) => {
          alert('글 등록에 성공했어요!')

          // console.log('[CommunityCreate.vue] 글 등록에 성공했어요!')
          // console.log('[CommunityCreate.vue] response: ', response)

          let service = this.$route.params.service
          let component = ''

          if (service === 'library') {
            component = 'ArticleRead'
          } else {
            component = 'CommunityRead'
          }

          this.$router.push({
            name: component,
            params: {
              service: response.data.service,
              number: response.data.number.toString()
            },
            query: {
              page: '1'
            }
          })
        })
        .catch((error) => {
          // console.log('[CommunityCreate.vue] 글 등록에 실패했어요.. ㅠㅜ')
          // console.log('[CommunityCreate.vue] error: ', error)
          // console.log('[CommunityCreate.vue] error.message: ', error.message)
          // console.log('[CommunityCreate.vue] error.response: ', error.response)
          // console.log('[CommunityCreate.vue] error.response.data: ', error.response.data)
          // console.log('[CommunityCreate.vue] error.response.status: ', error.response.status)

          if (error.response.status === 401) {
            alert('로그인해주세요!')
          } else {
            alert('글 등록에 실패했어요.. ㅠㅜ')

            // console.log('[CommunityCreate.vue] 글 등록에 실패했어요.. ㅠㅜ')
            // console.log('[CommunityCreate.vue] error.response.data: ', error.response.data)
            // console.log('[CommunityCreate.vue] error.response.data.message: ', error.response.data.message)
          }
        })
    }
  }
}
</script>

<style scoped></style>
