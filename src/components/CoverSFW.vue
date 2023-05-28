<template>
  <router-link :to="`/work/${workid}`">
    <q-img
      :src="coverUrl"
      :ratio="4/3"
      :img-class="imgClass"
      style="max-width: 560px;"
      transition="fade"
      @mouseover="toggleBlurFlag()"
      @mouseout="toggleBlurFlag()"
    >
      <div class="absolute-top-left transparent" style="padding: 0;">
        <q-chip dense square color="brown" text-color="white" class="q-ma-sm">
          {{`${rjcode}`}}
        </q-chip>
      </div>

      <div :v-if="release" class="absolute-bottom-right" style="padding: 5px;">
        {{release}}
      </div>
    </q-img>
  </router-link>   
</template>

<script>
import { formatProductID } from 'src/utils/format-id'

export default {
  name: 'CoverSFW',

  props: {
    workid: {
      type: Number,
      required: true
    },
    
    nsfw: {
      type: Boolean,
      default: true
    },

    release: {
      required: true
    }
  },

  data () {
    return {
      blurFlag: true,
    }
  },

  computed: {
    coverUrl () {
      // 从 LocalStorage 中读取 token
      const token = this.$q.localStorage.getItem('jwt-token') || ''
      return this.workid ? `/api/cover/${this.workid}?token=${token}` : ""
    },

    rjcode () {
      return formatProductID(this.workid, 'RJ')
    },

    imgClass () {
      if (this.$q.platform.is.mobile) {
        // 在移动设备上图片直接显示
        return ""
      } else {
        if (!this.nsfw) {
          // 在PC上SFW的图片直接显示
          return ""
        } else {
          // 在PC上NSFW的图片鼠标悬停显示
          return this.blurFlag ? "blur-image" : ""
        }
      }
    }
  },

  methods: {
    toggleBlurFlag () {
      this.blurFlag = !this.blurFlag
    }
  }
}
</script>

<style lang="scss">
  .blur-image {
    filter: blur(10px);
  }
</style>
