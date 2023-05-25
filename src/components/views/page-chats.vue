<template>
  <div class="page">
    <el-card shadow="never" class="vel_card_override">
      <el-scrollbar height="450px">
        <div v-for="i in chat_list.length " :key="i">
          <div class="scrollbar-ai-item" v-if="chat_list[i-1]['type'] === 'ai'">{{
              chat_list[i - 1]['text']
            }}
          </div>
          <div class="scrollbar-user-item" v-else>{{ chat_list[i - 1]['text'] }}</div>
        </div>
      </el-scrollbar>
      <el-input
          v-model="textarea"
          maxlength="30"
          placeholder="聊点什么吧~"
          show-word-limit
          type="textarea"
          style="margin-top: 40px"
          :input-style="{fontFamily: 'Microsoft YaHei', fontSize: '18px'}"
      />
      <div v-if="textarea.length>0 && chat_list.length<17">
        <el-button type="success" @click="submitChat"
                   style="width: 100px;margin: 10px 46%;">
          发送
        </el-button>

      </div>
      <el-button v-if="chat_list.length>=17" type="info" @click="save_history_chats"
                 style="width: 100px;margin: 10px 46%;">
        新的对话
      </el-button>
    </el-card>
  </div>
</template>

<script>
import {ElInput, ElLoading, ElMessage} from "element-plus";
import {reqSendChat, reqGetProcess} from "/src/api"

export default {
  components: {
    ElInput,

  },
  data() {
    return {
      textarea: "",

      chat_list: [{
        text: '您好~我是ChatRat，您的智能AI小助手。欢迎向我提问喵~',
        type: 'ai',
      }],
    };
  },

  methods: {
    // 提交提问，等待服务器回答
    submitChat() {
      function sleep(time) {
        return new Promise(resolve => setTimeout(resolve, time))
      }

      reqSendChat(this.textarea).then(async (response) => {

        let data = response.data;
        console.log(response.data);
        const loading = ElLoading.service({
          lock: true,
          text: 'Loading',
          background: 'rgba(0, 0, 0, 0.7)',
        })
        this.chat_list.push({
          text: this.textarea,
          type: 'user',
        })
        this.textarea = ''
        // status为0时说明后端出现问题
        if (data.status === 0) {
          ElMessage({
            message: '提问失败，服务器出现问题！',
            type: 'error',
          })
        } else if (data.status === 1) {
          this.chat_list.push({
            text: data.answer,
            type: 'ai',
          })
          ElMessage({
            message: 'ChatRats已回答~',
            type: 'success',
          })
        } else {
          let process_data = {};
          while (process_data.status !== 1) {
            await sleep(3000);
            reqGetProcess().then((response) => {
              process_data = response.data;
            })
          }
          this.chat_list.push({
            text: process_data.answer,
            type: 'ai',
          })
          ElMessage({
            message: 'ChatRats已回答~',
            type: 'success',
          })
        }
        loading.close();
        if (this.chat_list.length >= 17) {
          ElMessage({
            message: '当前对话轮数已达上限，开始新的对话吧~',
            type: 'warning',
          })
        }

      }).catch(function () {
        ElMessage({
          message: '上传失败，请检查网路！',
          type: 'error',
        })
      });


      // 前端测试使用
      // this.chat_list.push({
      //   text: '你说的对，但是《原神》是由米哈游自主研发的一款全新开放世界冒险游戏。游戏发生在一个被称作「提瓦特」的幻想世界，在这里，被神选中的人将被授予「神之眼」，导引元素之力。你将扮演一位名为「旅行者」的神秘角色，在自由的旅行中邂逅性格各异、能力独特的同伴们，和他们一起击败强敌，找回失散的亲人——同时，逐步发掘「原神」的真相。因为你的素养很差，我现在每天玩原神都能赚150原石，每个月差不多5000原石的收入， 也就是现实生活中每个月5000美元的收入水平，换算过来最少也30000人民币，虽然我 只有14岁，但是已经超越了中国绝大多数人(包括你)的水平，这便是原神给我的骄傲的资 本。毫不夸张地说，《原神》是miHoYo迄今为止规模最为宏大，也是最具野心的一部作品。即便在经历了8700个小时的艰苦战斗后，游戏还有许多尚未发现的秘密，错过的武器与装备，以及从未使用过的法术和技能。尽管游戏中的战斗体验和我们之前在烧机系列游戏所见到的没有多大差别，但游戏中各类精心设计的敌人以及Boss战已然将战斗抬高到了一个全新的水平。就和几年前的《 塞尔达传说 》一样，《原神》也是一款能够推动同类游戏向前发展的优秀作品。',
      //   type: 'ai',
      // })
      // if (this.chat_list.length >= 17) {
      //   ElMessage({
      //     message: '当前对话轮数已达上限，开始新的对话吧~',
      //     type: 'warning',
      //   })
      // }

    },
    save_history_chats() {
      this.$store.state.history_chats_list.push(this.chat_list)
      this.chat_list = [{
        text: '让我们开始新的对话吧~',
        type: 'ai',
      }]
      ElMessage({
        message: '已开启新的对话，之前的对话请查看历史对话模块~',
        type: 'success',
      })

    },
  }
}
</script>

<style scoped>
.page {
  padding: 20px;
}

.vel_card_override {
  height: calc(100vh - 90px - 20px - 20px - 2px);
}

.scrollbar-ai-item {
  display: flex;
  font-size: 16px;
  justify-content: flex-start;
  padding: 12px;
  width: 400px;
  margin: 10px;
  text-align: left;
  border-radius: 4px;
  background: var(--el-color-primary-light-9);
  color: #2dc4af;
  white-space: pre-wrap;
}

.scrollbar-user-item {
  display: flex;
  font-size: 16px;
  justify-content: flex-start;
  padding: 12px;
  width: 400px;
  margin: 10px 10px 10px auto;
  text-align: right;
  border-radius: 4px;
  background: pink;
  color: #5814a1;
  white-space: pre-wrap;
}
</style>