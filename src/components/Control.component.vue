<template>
    <div class="control" ref="control" id="control_area">
      <h4 v-if="connect" style="color: green">Соединение установлено</h4>
      <input v-model="clientPass"/>
      <button @click="Connect">отправить пароль</button>
    </div>
</template>
<script>
export default {
    name :'Control',
    data: function(){
        return {
            ws : null,
            connect : false,
            clientPass : null,
        }
    },
    methods : {
        Connect: function() {
            this.ws.send(JSON.stringify({type : 'connect', pass : this.clientPass}))
        },
        SetCoords : function() {
            const self = this; 
            const coverage  = document.getElementById('control_area');
            coverage.addEventListener('mousemove', function( event ){
                let arr= [event.clientX, event.clientY, document.body.clientWidth];
                let req = { type : 'coords', coords : arr }
                    self.ws.send(JSON.stringify( req ))
            });
            coverage.addEventListener('click', function( event ) {
                let x = event.clientX;
                let y = event.clientY;
                self.ws.send(JSON.stringify({ type : 'click', coordsElement : [ x, y ]}))
            });
            coverage.addEventListener('scroll', function() {
                let sizeScroll = window.scrollY;
                self.ws.send(JSON.stringify({ type : 'scroll', value : sizeScroll }))
            })
        },
        ClientWidth : function( clientWidth ) {
            this.$refs.control.style.width = clientWidth + 'px'
        }
    },
    mounted : function() {
        const self = this;
        this.ws = new WebSocket('ws://localhost:8081');
        this.ws.onmessage = function( message ) {
            let res = JSON.parse(message.data);
            let type = res[0];
            switch ( type ) {
                case 'success' : self.SetCoords(); break;
                case 'clientWidth' : self.ClientWidth( res[1] ); break;
            }
        }
    }
}
</script>

<style scoped>
    .control {
        position: absolute;
        width: 100%;
        height: 300%;
        top: 0;
        left: 0;
        background-color: white;
    }
</style>


