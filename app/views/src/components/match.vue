<template>
  <div v-loading="loading">
    <el-row style="margin-top: 20px; margin-bottom: 20px">
      <el-card shadow="never">
        <el-row>
          <el-col :span="12">
            <div class="item-title">对局人数:</div>
            <div class="item-text">{{parties.length}}</div>
          </el-col>
          <el-col :span="12">
            <div class="item-title">对局状态:</div>
            <div v-if="status === $consts.codeStat.pending" class="item-text" style="color: gray">等待中</div>
            <div v-else-if="status === $consts.codeStat.compiling" class="item-text" style="color: orange">编译中</div>
            <div v-else-if="status === $consts.codeStat.running" class="item-text" style="color: orange">运行中</div>
            <div v-else-if="status === $consts.codeStat.accepted" class="item-text" style="color: green">已结束</div>
            <div v-else class="item-text" style="color: red">系统错误</div>
          </el-col>
        </el-row>
      </el-card>
    </el-row>
    <el-row style="margin-top: 20px; margin-bottom: 20px" :gutter="10">
      <el-col v-for="(item, index) in parties" :key="index" :span="8">
        <el-card shadow="hover">
          <el-avatar
            :src="$axios.defaults.baseURL + '/user/' + item.participant.handle + '/avatar'"
            :size="80"></el-avatar>
          <div style="font-size: 16px; font-weight: 600">{{item.participant.nickname}}</div>
          <router-link
            style="font-size: 16px; color: #409EFF; text-decoration: none"
            :to="{path: '/profile', query: {handle: item.participant.handle}}"
          >{{item.participant.handle}}</router-link>
          <div>
            <div style="font-size: 14px; display: inline">提交ID:
            <router-link
              v-if="myRole===$consts.role.moderator"
              style="color: #409EFF; text-decoration: none"
              :to="{path: '/submission_info', query: {cid: cid, sid: item.id}}"
            >{{item.id}}</router-link>
            <span v-else>{{item.id}}</span>
            |</div>
            <el-link
              style="color: #409EFF; text-decoration: none"
              :href="$axios.defaults.baseURL + '/contest/' + cid + '/match/' + mid + '/log/' + index"
            >对局日志</el-link>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <el-row>
      <el-card shadow="never" body-style="min-height: 480px">
        <div align="left" style="font-size: 24px; font-weight: 600">比赛回放</div>
        <div>
          <iframe
            :src="$axios.defaults.baseURL + '/contest/' + cid + '/match/' + mid + '/playback'"
            style="height: 1000px; width: 100%; margin-top: 20px" scrolling="yes"></iframe>
        </div>
      </el-card>
    </el-row>
    <!-- <el-row>
      <el-card>
        <el-row>
          <el-col :span="6">
            <el-button style="width: 80%">上一回合</el-button>
          </el-col>
          <el-col :span="6">
            <el-button style="width: 80%" type="primary" @click="onstage=fake">暂停/开始</el-button>
          </el-col>
          <el-col :span="6">
            <el-button style="width: 80%">下一回合</el-button>
          </el-col>
          <el-col :span="6">
            <el-button style="width: 80%" type="text">下载录像文件</el-button>
          </el-col>
        </el-row>
      </el-card>
    </el-row> -->
  </div>
</template>

<script>
export default {
  name: 'match',
  created () {
    this.cid = this.$route.query.cid
    this.mid = this.$route.query.mid
    this.getInfo()
  },
  data () {
    return {
      loading: false,
      parties: [],
      mid: '',
      cid: '',
      status: 0,
      myRole: -1,
      fake: '',
      onstage: ''
    }
  },
  methods: {
    getInfo () {
      this.parties = []
      this.loading = true
      this.$axios.get(
        '/contest/' + this.cid + '/match/' + this.mid
      ).then(res => {
        console.log(res.data.report)
        this.parties = res.data.parties
        this.status = res.data.status
        this.$axios.get(
          '/contest/' + this.cid + '/info'
        ).then(info => {
          this.loading = false
          this.myRole = info.data.my_role
        })
      }).catch(err => {
        console.log(err)
        this.loading = false
        this.$message.error('加载失败')
      })
    }
  }
}
</script>

<style scoped>
.item-title{
  display: inline;
  color: gray;
  font-weight: 600;
}
.item-text{
  display: inline;
  color: black;
  font-weight: 400;
}
</style>
