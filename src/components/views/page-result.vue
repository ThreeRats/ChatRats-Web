<template>
    <div class="page">
        <el-card shadow="never" class="vel_card_override">
            <div v-if="history_chats_list.length>0">
                <div v-if="show_chat">
                    <el-scrollbar height="500px">
                        <div v-for="i in chat_list.length " :key="i">
                            <div class="scrollbar-ai-item" v-if="chat_list[i-1]['type'] === 'ai'">{{
                                chat_list[i - 1]['text']
                                }}
                            </div>
                            <div class="scrollbar-user-item" v-else>{{ chat_list[i - 1]['text'] }}</div>
                        </div>
                    </el-scrollbar>
                    <el-button type="info" @click="toShowChatList"
                               style="width: 100px;margin: 20px 46%;">
                        返回
                    </el-button>
                </div>
                <div v-else>
                    <div class="history_chats_table">
                        <div v-for="i in Math.min(8,history_chats_list.length-(now_page-1)*8)" :key="i"
                             class="chatItem">
                            <el-button
                                    type="warning"
                                    @click="toShowChat((now_page - 1) * 5 + i - 1)"
                            >{{ history_chats_list[(now_page - 1) * 8 + i - 1][1]['text'] }}
                            </el-button
                            >
                        </div>
                    </div>
                    <div id="changePages">
                        <el-button v-if="now_page !== 1" type="success" @click="now_page--"
                        >上一页
                        </el-button
                        >
                        <el-button v-else type="info" disabled>上一页</el-button>
                        <el-button
                                v-if="now_page*8 < history_chats_list.length"
                                type="success"
                                @click="now_page++"
                        >下一页
                        </el-button
                        >
                        <el-button v-else type="info" disabled>下一页</el-button>
                    </div>
                </div>


            </div>
            <h3 v-else> 请先完成一次对话...</h3>
        </el-card>
    </div>
</template>

<script>
export default {
    data() {
        return {
            history_chats_list: this.$store.state.history_chats_list,
            now_page: 1,  // 当前所在页数
            show_chat: false,
            chat_list: [],
        };
    },
    // 前端测试使用
    // mounted() {
    //     this.history_chats_list = []
    //     for (let i = 0; i <= 42; i++) {
    //         let temp_list = [{
    //             text: '您好~我是ChatRat，您的智能AI小助手。欢迎向我提问喵~',
    //             type: 'ai',
    //         }]
    //         for (let j = 0; j < 8; j++) {
    //             temp_list.push({
    //                 text: '玩原神玩的？' + i,
    //                 type: 'user'
    //             })
    //             temp_list.push({
    //                 text: '你说的对，但是《原神》是由米哈游自主研发的一款全新开放世界冒险游戏。游戏发生在一个被称作「提瓦特」的幻想世界，在这里，被神选中的人将被授予「神之眼」，导引元素之力。你将扮演一位名为「旅行者」的神秘角色，在自由的旅行中邂逅性格各异、能力独特的同伴们，和他们一起击败强敌，找回失散的亲人——同时，逐步发掘「原神」的真相。因为你的素养很差，我现在每天玩原神都能赚150原石，每个月差不多5000原石的收入， 也就是现实生活中每个月5000美元的收入水平，换算过来最少也30000人民币，虽然我 只有14岁，但是已经超越了中国绝大多数人(包括你)的水平，这便是原神给我的骄傲的资 本。毫不夸张地说，《原神》是miHoYo迄今为止规模最为宏大，也是最具野心的一部作品。即便在经历了8700个小时的艰苦战斗后，游戏还有许多尚未发现的秘密，错过的武器与装备，以及从未使用过的法术和技能。尽管游戏中的战斗体验和我们之前在烧机系列游戏所见到的没有多大差别，但游戏中各类精心设计的敌人以及Boss战已然将战斗抬高到了一个全新的水平。就和几年前的《 塞尔达传说 》一样，《原神》也是一款能够推动同类游戏向前发展的优秀作品。',
    //                 type: 'ai',
    //             })
    //         }
    //         this.history_chats_list.push(temp_list)
    //     }
    // },
    methods: {
        toShowChat(index) {
            this.chat_list = this.history_chats_list[index]
            this.show_chat = true

        },
        toShowChatList() {
            this.chat_list = []
            this.show_chat = false
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

.chatItem {
    height: 40px;
    display: flex;
    justify-content: space-around;
    margin-bottom: 20px;
}

#changePages {
    display: flex;
    justify-content: space-around;
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