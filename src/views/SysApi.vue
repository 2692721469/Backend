<template>
    <h1>SysApi.vue</h1>
</template>

<script>
import mqtt from 'mqtt'

export default {
    data() {
        return {
            // message: '',
            client: "",
            message: "",
            logs: [],
        };
    },
    methods: {
        /**
     * @name:初始化mqtt
     * @msg:
     * @param {*}
     * @return {*}
     */
        initMqtt() {
            // const mqtt = require('mqtt')
            const url = 'http://39.101.180.14:8083/mqtt'

            // 创建客户端实例
            const options = {
                // Clean session
                clean: true,
                connectTimeout: 4000,
                // 认证信息
                clientId: 'emqx_test',
                username: 'emqx_test',
                password: 'emqx_test',
            }
            const client = mqtt.connect(url, options)
            client.on('connect', function () {
                console.log('Connected')
                // 订阅主题
                client.subscribe('test', function (err) {
                    if (!err) {
                        // 发布消息
                        client.publish('test', 'Hello mqtt')
                    }
                })
            })

            // 接收消息
            client.on('message', function (topic, message) {
                // message is Buffer
                console.log(message.toString())
                client.end()
            })
        },
    },
    mounted() {
        this.initMqtt();
        this.mqttReceive();
    },
};
</script>
