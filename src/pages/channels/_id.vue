<template lang="pug">
  v-container(fluid)
    v-row.ma-n2
      v-col(cols='12')
        ChannelPanel(:channel='channel' imageWidth='285')
    
      v-col(cols='12' md='6')
        v-row(no-gutters)
          v-col(cols='12')
            v-card
              v-card-text
                ChannelButtonPanel(:channel='channel')

        v-row
          v-col(cols='12')
            v-card
              v-card-text
                ChannelInfoTable(:channel='channel')

      v-col(cols='12' md='6')
        v-card
          v-card-title 一週間の動向
          v-card-text
            ChannelChart(:stats='stats')

    v-col(cols='12')
      v-card
        v-card-title アクティビティ
        v-card-text
          VideoList(:videos='videos' showGrid=false :imageWidth='200')

</template>

<script>
import apiHandler from '@/mixins/apiHandler'

import ChannelPanel from '@/components/channels/ChannelPanel'
import ChannelButtonPanel from '@/components/channels/ChannelButtonPanel'
import ChannelInfoTable from '@/components/channels/ChannelInfoTable'
import ChannelChart from '@/components/channels/ChannelChart'
import VideoList from '@/components/videos/VideoList'

export default {
  components: { ChannelPanel, ChannelButtonPanel, ChannelInfoTable, ChannelChart, VideoList },
  
  mixins: [apiHandler],

  data: function () {
    return {
      channel: {},
      videos: [],
      stats: []
    }
  },

  created: async function() {
    await this.getDataFromApi()
  },

  methods: {
    async getDataFromApi () {
      this.apiHandler(async () => {
        const id = this.$route.params.id
        const { data } = await this.$http.get(`channels/${id}`)
        const { data: stats } = await this.$http.get(`channels/${id}/stats`) // 統計情報
        const { data: { items: videos }} = await this.$http.get(`videos`, { // アクティブ video
          params: {
            channel: id,
            sort: 'startTime',
            order: 'desc',
            size: 3
          }
        })

        this.channel = data
        this.stats = stats
        this.videos = videos
      })
    }
  }

}
</script>