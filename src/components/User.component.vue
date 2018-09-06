<template>
    <div class="user" ref="user" @resize="Resize()">
        <h3 v-if="id">ID : {{id}}</h3>
        <div class="cursor" ref="cursor" ></div>
        <button @click="clickButton">генерировать пароль</button>
        <h3>{{password}}</h3>
    </div>
</template>

<script>
export default {
    name: 'User',
    data : function() {
        return {
            ws : null,
            password : null,
            id : null,
            virtualMouse : false,
            observerResize : false,
            observerScroll : false
        }
    },
    methods : {
        clickButton : function() {
            this.password = this.generateID();
            this.ws.send(JSON.stringify({type : 'newpass', pass : this.password}))
        },
        generateID : function () {
            return Math.floor((1 + Math.random()) * 0x10000)
            .toString(16)
            .substring(1);
        },
        SetCoords : function( arr ) {
            !this.observerResize ? this.ObserverResize() : false;
            !this.virtualMouse ? this.virtualMouse = true : false;
            this.$refs.cursor.style.top = arr[1] + 'px';
            this.$refs.cursor.style.left = arr[0] + 'px';
            // this.$refs.cursor.style.transform = `translate(${arr[0]}px,${arr[1]}px)`
        },
        ClickElement : function( coordsElement ){
            let x = coordsElement[0];
            let y = coordsElement[1];
            let element = document.elementFromPoint( x,y );
            element.click();
        },
        ObserverResize : function() {
            let sizeWin = document.body.clientWidth;
            this.ws.send(JSON.stringify({ type : 'clientWidth', value : sizeWin }));
            window.addEventListener('resize', function( event ) {
                this.ws.send(JSON.stringify({ type : 'clientWidth', value : document.body.clientWidth }))
            });
            this.observerResize = true;
        },
        ObserverScroll : function() {
            let sizeScroll = window.scrollY;
            this.ws.send(JSON.stringify({ type : 'scroll', value : sizeScroll }))
        },
        SetScroll : function( sizeScroll ) {
            window.scrollTo(0, sizeScroll)
        }

    },
    mounted : function() {
        const self = this;
        this.ws = new WebSocket('ws://localhost:8081');
        this.ws.onmessage = function( message ) {
            var res = JSON.parse(message.data);
            let type = res[0];
            switch (type) {
                case 'confirm' : self.id = res[1]; break;
                case 'info' : break;
                case 'coords' : self.SetCoords( res[1] ); break;
                case 'click' : self.ClickElement( res[1] ); break;
                case 'scroll' : self.SetScroll( res[1] ); break;
            }
        }
  }
}
</script>

<style scoped>
    h3 {
        font-size: 15px;
        padding-left: 15px;
    }

    .cursor {
        width: 15px;
        height: 15px;
        position: fixed;
        border-radius: 50%;
        background-color: green;
    }

    .user {
        position: absolute;
        width: 100%;
        height: 300%;
        top: 0;
        left: 0;
        background-color: white;
    }
</style>


