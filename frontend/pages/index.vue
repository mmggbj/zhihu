<template>
  <div id="main-container" class="inner">
    <DashBoard></DashBoard>

    <!-- RECOMMEND LIST BEGIN -->
    <main>

      <!-- RECOMMEND ANSWER BEGIN -->
      <div v-for="(answer, index) of recommendList" class="recommend" :key="index"
           v-if="(recommendList && recommendList.length)">

        <div class="feed-title">
          <span>
            热门内容, 来自:
            <span>{{ answer.topic }}</span>
          </span>
        </div>

        <div class="author-info" v-if="!answer.anonymous">
          <div>{{ answer.author_name }}</div>
          <div>{{ answer.author_desc }}</div>
        </div>
        <div class="author-info" v-if="answer.anonymous">
          <div>匿名用户</div>
        </div>

        <div class="question-title">
          <h2>
            <router-link :to="`questions/${answer.question_id}`" target="_blank">
              {{ answer.question_title }}
            </router-link>
          </h2>
        </div>

        <div class="answer-content" v-show="!contentStatus[index]"
        @click="toggleContent(index)">
          <span>{{ cutContent(answer.text) }}</span>
          <button type="button">
            阅读全文
            <icon name="angle-down" scale="1.1"></icon>
          </button>
        </div>

        <!-- TOGGLE CONTENT -->
        <div class="answer-content" v-show="contentStatus[index]">
          <span>{{ answer.text }}</span>
          <div class="answer-publish" v-show="contentStatus[index]">
            <span>发布于 {{ pubTime(answer.publish) }}</span>
          </div>
        </div>

        <div class="answer-actions-outside">
          <AnswerAction :vote="answer.vote" :answerId="answer.id" :answerIndex="index"
          :voteStatus="voteStatus" :link="answer.question_id" :favStatus="favStatus">
          </AnswerAction>
          <button type="button" class="read-less" v-show="contentStatus[index]"
          @click="toggleContent(index)">
            收起
            <icon name="angle-up" scale="1.1"></icon>
          </button>
        </div>

      </div>
      <!-- RECOMMEND ANSWER END -->

      <div class="recommend" v-if="!(recommendList && recommendList.length)">
        <span>还没有相关的提问...</span>
      </div>

    </main>
    <!-- RECOMMEND LIST END -->

    <!-- SIDE CONTAINER BEGIN -->
    <section id="side-container">
      <ul class="nav-list">
        <li>
          <router-link :to="`collection`" target="_blank">
            <icon name="star" scale="1"></icon>
            <span>我的收藏</span>
          </router-link>
        </li>
        <li>
          <a href="#" title="未开发">
            <icon name="question-circle" scale="1"></icon>
            <span>我关注的问题</span>
          </a>
        </li>
        <li>
          <a href="#" title="未开发">
            <icon name="user-plus" scale="1"></icon>
            <span>我的邀请</span>
          </a>
        </li>
      </ul>
    </section>
    <!-- SIDE CONTAINER END -->

  </div>
</template>

<script>
import {fetchAnswers, fetchUserVote, fetchfav} from '../api/api'
import AnswerAction from '../components/Home/AnswerAction'
import DashBoard from '../components/Home/DashBoard'
export default {
  layout: 'home',
  head: {
    title: "首页 - 知乎"
  },
  components: {
    DashBoard,
    AnswerAction,
  },
  middleware: 'checkAuth',
  data () {
    return {
      recommendList: [],
      contentStatus: [],
      voteStatus: [],
      favStatus: [],
    }
  },
  methods: {
    // 获取回答JSON & 获取回答VOTE状态
    fetchData () {
      fetchUserVote('')
      .then (res => {
        this.voteStatus = res.data
      })
      fetchAnswers()
      .then (res => {
        this.recommendList = res.data.results
      })
      fetchfav()
      .then (res => {
        this.favStatus = res.data
      })
    },
    // 问题文本截取
    cutContent (text) {
      if (text.length > 80) {
        return text.slice(0, 80) + '...';
      } else {
        return text;
      }
    },
    // 时间显示
    pubTime (time) {
      return time.slice(0,10)
    },
    // 开关READ MORE
    toggleContent (index) {
      this.$set(this.contentStatus, index, !this.contentStatus[index])
    }
  },
  created () {
    this.fetchData ()
  }
}
</script>

<style lang="scss" scoped>
@import '../assets/css/home.scss';
</style>
