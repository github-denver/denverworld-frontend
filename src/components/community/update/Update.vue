<template>
  <form method="post" enctype="multipart/form-data" @submit.prevent="submit" novalidate>
    <design-select
      :attribute="{
        className: 'full'
      }"
      :select="select"
      @parentChange="onChange"
    />

    <div class="grp_conts">
      <div class="read_head">
        <Input
          :attribute="{
            type: 'text',
            id: 'subject',
            title: '제목',
            className: 'underline',
            value: subject
          }"
          v-model="subject"
        />
      </div>

      <vue-editor useCustomImageHandler @image-added="handleImageAdded" v-model="content"></vue-editor>

      <div class="read_foot">
        <Input
          :attribute="{
            type: 'file',
            id: 'thumbnail',
            title: '대표 이미지',
            className: 'full'
          }"
          v-model="thumbnail"
          @parentChange="onPictureChange"
        />
      </div>
    </div>

    <div class="grp_gravity">
      <div class="inner_half">
        <router-link
          :to="{
            name: 'CommunityList',
            params: {
              service: service,
              number: page.toString()
            }
          }"
          class="link_g"
          >취소하기</router-link
        >
      </div>

      <div class="inner_half">
        <rectangle-button :attribute="{ type: 'submit', className: 'btn_action' }">완료하기</rectangle-button>
      </div>
    </div>
  </form>
</template>

<script>
import { mapActions } from 'vuex'
import { VueEditor } from 'vue2-editor'

import DesignSelect from '@/components/common/Select'
import Input from '@/components/unit/Input'
import RectangleButton from '@/components/unit/RectangleButton'

import api from '@/api'

export default {
  name: 'Update',
  components: { DesignSelect, Input, RectangleButton, VueEditor },
  props: {
    service: {
      type: String,
      required: true
    },
    post: {
      type: Object
      // required: true,
    }
  },
  data() {
    return {
      select: {
        category: '',
        data: [
          {
            optgroup: {
              text: '새소식',
              value: 'news',
              option: [
                {
                  text: '많이 본 소식',
                  value: 'popular'
                },
                {
                  text: '컴퓨터 &amp; 하드웨어',
                  value: 'hardware'
                },
                {
                  text: '모바일 &amp; 태블릿',
                  value: 'mobile'
                },
                {
                  text: '신작 게임 &amp; 업데이트 동영상',
                  value: 'game'
                },
                {
                  text: '넷플릭스 출시 예정 &amp; 신작 동영상',
                  value: 'video'
                },
                {
                  text: '공지사항 &amp; 업데이트',
                  value: 'notice'
                }
              ]
            }
          },
          {
            optgroup: {
              text: '커뮤니티',
              value: 'community',
              option: [
                {
                  text: '톡톡 한마디',
                  value: 'talk'
                },
                {
                  text: '이미지 게시판',
                  value: 'gallery'
                }
              ]
            }
          },
          {
            optgroup: {
              text: '자료실',
              value: 'library',
              option: [
                {
                  text: '음악',
                  value: 'music'
                }
              ]
            }
          }
        ]
      },
      category: '',
      subject: '',
      content: '',
      thumbnail: '',
      picture: {
        files: null,
        result: null
      },
      page: 1
    }
  },
  created() {
    const post = async () => {
      await this.fetchPost({
        category: this.$route.params.service,
        number: this.$route.params.number,
        select: this.$route.select,
        keyword: this.$route.keyword
      }).catch((error) => {
        alert(error)

        // console.error(error)

        // console.log('[CommunityUpdate.vue] created() → error: ', error)
        // console.log('[CommunityUpdate.vue] created() → error.response: ', error.response)

        this.$router.back()
      })

      await data()
    }

    const data = () => {
      this.category = this.post.category
      this.subject = this.post.subject
      this.content = this.post.content
    }

    if (this.post === null) {
      // console.log('[CommunityUpdate.vue] created() → 수정할 정보를 읽어들이는 중입니다!')

      post()
    } else {
      // console.log('[CommunityUpdate.vue] created() → 전달받은 정보를 읽어들이는 중입니다!')

      data()
    }

    this.select.category = this.service

    this.page = this.$route.query.page
  },
  methods: {
    ...mapActions(['fetchPost']),
    onPictureChange(payload) {
      this.picture.files = payload.get('files')
      this.picture.result = payload.get('result')
    },
    submit() {
      const { category, subject, content } = this

      const number = this.post.number
      const thumbnail = this.picture.files

      const formData = new FormData()
      formData.append('number', number)
      formData.append('category', category)
      formData.append('subject', subject)
      formData.append('content', content)
      formData.append('thumbnail', thumbnail)

      this.$emit('parentSubmit', formData)
    },
    onChange(payload) {
      const { text, value } = payload

      this.category = value
    },
    handleImageAdded: function(file, Editor, cursorLocation, resetUploader) {
      const formData = new FormData()
      formData.append('picture', file)

      api
        .post(`/api/board/${this.category}/upload`, formData)
        .then((result) => {
          const folder = 'uploads'
          const url = result.data.image.imageurl
          // console.log('[CommunityUpdate.vue] methods() → handleImageAdded → url: ', url)

          const name = result.data.image.filename
          // console.log('[CommunityUpdate.vue] methods() → handleImageAdded → name: ', name)

          Editor.insertEmbed(cursorLocation, 'image', `${this.path}${url}`)

          resetUploader()
        })
        .catch((error) => {
          alert('이미지 업로드에 실패했어요.. ㅠㅜ')

          // console.log(error)
        })
    }
  }
}
</script>

<style scoped></style>
