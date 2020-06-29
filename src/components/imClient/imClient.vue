<!-- im客户端 入口 -->
<template>
    <div class="imClient-wrapper">
        <div class="imClient-inner">
            <div class="imClient-header">
                <div class="name-wrapper position-v-mid">
                <el-button @click="QRscan()" >點我拍照</el-button>
                <el-button @click="initializeLiff()" >點我INIT</el-button>
                </div>
            </div>

        </div>
    </div>
</template>

<script >
import axios from 'axios';

export default {
    components: {
    },
    data() {
        return {
            
            socket: null,
            chatInfoEn: {
                chatState: 'agent', // chat状态；robot 机器人、agent 客服
                inputContent: '', // 输入框内容
                msgList: [], // 消息列表
                state: 'on', // 连接状态;on ：在线；off：离线
                lastMsgShowTime: null // 最后一个消息的显示时间
            }, // 会话信息，包括聊天记录、状态
            clientChatEn: {
                clientChatId: '', // UserID
                clientChatName: '',// User name
                avatarUrl: 'static/image/im_client_avatar.png' //User ava.
            }, // 当前账号的信息
            serverChatEn: {
                serverChatName: '', 
                avatarUrl: ''
            }, // 服务端chat信息
            robotEn: {
                robotName: '小旺',
                avatarUrl: 'static/image/im_robot_avatar.png'
            },
            faqSelected: '-1',
            inputContent_setTimeout: null, // 输入文字时在输入结束才修改具体内容
            selectionRange: null, // 输入框选中的区域
            shortcutMsgList: [], // 聊天区域的快捷回复列表
        };
    },
    computed: {},
    watch: {},
    methods: {
        QRscan(){
        let cmp = this;
        if (!liff.isInClient()) {
        sendAlertIfNotInClient();
        } else {
    	if (liff.scanCode) {
	        liff.scanCode().then(result => {
	            // e.g. result = { value: "Hello LIFF app!" }
	            const stringifiedResult = JSON.stringify(result);
	            document.getElementById('scanQrField').textContent = stringifiedResult;
	            toggleQrCodeReader();
                 cmp.$message('text ='+stringifiedResult);
                 cmp.$message('id ='+cmp.$data.clientChatEn.clientChatId);
                axios.post("https://theflowchat.com:5001/test41",
				    {   
                    QR:stringifiedResult,
                    UserID:cmp.$data.clientChatEn.clientChatId
                    }
                )
                .then(response =>(
                cmp.$message('response ='+response)
                ));

	        }).catch(err => {
	            document.getElementById('scanQrField').textContent = "scanCode failed!";
	        });
    	}
        }
        },

        initializeLiff() {
        let cmp = this;
        liff
            .init({
                liffId: '1654198211-eMqv2EW7'           
            })
            .then(() => {
           
                    alert('liff init success');
                liff.getProfile().then(function (profile) {
                    cmp.$data.clientChatEn.clientChatId = profile.userId;
                    cmp.$data.clientChatEn.clientChatName = profile.displayName;

                    alert('this is clientid'+cmp.$data.clientChatEn.clientChatId);  
                })    

                .catch(function (error) {
                    alert(error);
                    console.log('error', error); 
                });
            })
            .catch((err) => {   
                console.log(err);

             });

        }
    },
    mounted() {
        // window.addEventListener('load', this.initializeLiff);
        
    }


};
</script>

<style lang="less">
@import '../../common/css/base.less';

.imClient-wrapper {
   display:flex;
   flex-direction:column;
   height: 100%;
   width: 100%;
}

.imClient-inner {
    display:flex;
    flex-direction:column;
    width: 100%;
    height: 100%;
    overflow: hidden;
    box-shadow: 0 1px 5px 2px #ccc;
    .imClient-header {
        display:flex;
        justify-content:space-between;
        position: relative;
        height: 5%;
        min-height: 5%;
        box-sizing: border-box;
        background: #1072b5;
box-shadow: 0px 0px 16px -8px #000000;
-webkit-box-shadow: 0px 0px 16px -8px #000000;
-moz-box-shadow: 0px 0px 16px -8px #000000;
-o-box-shadow: 0px 0px 16px -8px #000000;
        font-size: 16px;
        
        color: #ffffff;
        .name-wrapper {
            margin-top: 2%;
            margin-left: 2%;
        }
    }
    .imClient-main {
        height: 95%;
        & > .item {
            float: left;
            height: 100%;
            border-top-width: 0px;
            border-right-width: 0px;
            box-sizing: border-box;
            &:last-child {
                border-right-width: 1px;
            }
        }
        & > .imClientChat-wrapper {
            display:flex;
            width: 100%;
            border-right: 1px solid #ccc;
        }
    }
}

// 信息区域
.imClientInfo-wrapper {
    width: 100%;
    height: 100%;
    background: #ffffff;
    .imClientInfo-notice-wrapper,
    .imClientInfo-faq-wrapper {
        .imClientInfo-item-header {
            font-weight: bolder;
            font-size: 16px;
            color: #1072b5;
            padding: 10px 15px 0;
        }
    }
    .imClientInfo-notice-wrapper {
        .imClientInfo-notice-main {
            padding: 0 15px;
            & > .link {
                margin: 10px 0;
                font-size: 12px;
                color: #000000;
            }
        }
    }
    .imClientInfo-faq-wrapper {
        height: 380px;
        border-top: 1px solid #ccc;
        .imClientInfo-faq-main {
            height: 100%;
            overflow-y: auto;
            overflow-x: hidden;
            .el-collapse {
                border: 0px;
                .el-collapse-item__header {
                    position: relative;
                    padding: 0px 15px;
                    font-size: 12px;
                    background: transparent;
                    color: #000000;
                    &.is-active {
                        color: #f7455d;
                    }
                    .el-collapse-item__arrow {
                        position: absolute;
                        left: 267px;
                    }
                }
                .el-collapse-item__wrap {
                    background: transparent;
                    .el-collapse-item__content {
                        font-size: 12px;
                        color: #959699;
                        padding: 0px 15px 10px;
                    }
                }
            }
        }
    }
}

// element-UI
.el-dialog {
    width: 500px;
    background: #ffffff;
    color: #000000;
}
</style>
