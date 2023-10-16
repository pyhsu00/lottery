<template>
    <div class="wrapper" @click="getRandom()" :class="{ dark: showani }">
        <span v-if="!start" class="greeting">Christmas Party</span>
        <div class="ani" v-if="showani2">
            <client-only>
                <lottie-player
                :src="`/light.json`"
                background="transparent"
                :options="lottie_options"
                >
                </lottie-player>
            </client-only>
        </div>
        <div class="ani cel" v-if="showani2">
            <client-only> 
                <lottie-player
                    :src="`/cel.json`"
                    background="transparent"  
                    :options="lottie_options2"
                >
                </lottie-player>
            </client-only>
        </div>
        <div class="ani cat"  v-if="showani3 && raw.length % 2 === 0">
            <client-only> 
                <lottie-player
                    :src="`/catty.json`"
                    background="transparent"  
                    :options="lottie_options3"
                >
                </lottie-player>
            </client-only>
        </div>
        <div class="ani black bt" v-if="showani3 && raw.length % 2 !== 0">
            <client-only> 
                <lottie-player
                    :src="`/blackcat.json`"
                    background="transparent"  
                    :options="lottie_options4"
                >
                </lottie-player>
            </client-only>
        </div>
        <div v-if="start" class="piechart" :class="{ dark: showani, start: start }">
            <div class="tri"></div>
            <div class="border"></div>
            <div class="pie">
                <div
                class="slice"
                :class="{'odd': raw.length % 2 !== 0, 'even': raw.length% 2 === 0}"
                v-for="index in raw.length"
                :key="index"
                :style="{
                    transform: `rotate(${ (360 * (index - 1)) / raw.length - 360 / raw.length / 2 + position }deg)`,
                }"
                >
                <div
                    class="before"
                    :style="{
                    transform: `rotate(${360 / raw.length}deg)`,
                   
                    }"
                ></div>
                <h4
                    :style="{
                    transform: `rotate(${
                        (-360 * (index - 1)) / raw.length - position
                    }deg)`,
                    }"
                >
                    {{ raw[index - 1] }}
                </h4>
                </div>
            </div>
            <div class="center"></div>
        </div>
        <div class="box">
            <div class="ball" v-for="(item, index) in pool" :key="index">
                <span class="order">{{ index+1 }}</span>
                {{ item }}</div>
        </div>
        <div class="result" v-show="showani2">{{ result }}</div>
        <div class="logo">
        </div>
    </div>
</template>


<style>
@import "~assets/style.min.css";
</style>

<script>
export default {
    data() {
        return {
            start: false,
            result: null,
            pool: [],
            raw: [],
            counter: 0,
            position: 0,
            speed: 1,
            timer: null,
            r: null,
            disabled: false,
            lottie_options: {
                autoplay: true,
                background: "none",
                width: "1000px",
                height: "1000px",
                loop: true,
            },
            lottie_options2: {
                autoplay: true,
                background: "none",
                width: "2000px",
                height: "2000px",
            },
            lottie_options3: {
                autoplay: true,
                background: "none",
                width: "700px",
                height: "700px",
            },
            lottie_options4: {
                autoplay: true,
                background: "none",
                width: "500px",
                height: "500px",
            },
            lottie_options_star: {
                autoplay: true,
                background: "none",
                width: "90px",
                height: "90px",
            },            
            showani: false,
            showani2: false,
            showani3:false
        };      
    },
    methods: {
        reset() {
            this.showani = false;
            this.showani2 = false;  
            this.raw.splice(this.r, 1);
            this.r = null;
            this.position = 0;
        },
        getPrize() {
            return new Promise((resolve) => {
                this.r = Math.floor(Math.random() * (this.raw.length - 1));
                this.result = this.raw[this.r];
                resolve(this.r);
            });
        },
        basicScroll(){
            return new Promise((resolve) => {
                let speed = 2;
                this.timer = setInterval(() => {
                    this.counter += Math.pow(2,speed) ;
                    this.position += Math.pow(2,speed) ;
                    switch(true){
                        case this.counter > 360 * 2.7 && this.counter < 360 * 2.8: 
                            speed = 1.7 
                            break;
                        case this.counter >= 360 * 2.8 && this.counter < 360 * 2.9: 
                            speed = 1.3
                            break;
                        case this.counter >= 360 * 2.9: 
                            speed = 1.1
                            break;
                    }
                    if (this.counter > 360*3) {
                        clearInterval(this.timer);
                        this.timer = null;
                        this.counter = 0;
                        this.position = 0;
                        resolve(true);
                    }
                }, 10);
                
            });
        },
        async getRandom() {
            if(!this.start){
                this.start = true
            }else{
                if (this.raw.length > 1 && !this.disabled) {
                    (this.pool.length > 0) && this.reset();
                    this.disabled = true;
                    let basic = await this.basicScroll();
                    this.showani3 = true;
                    if(basic){
                        let prize = await this.getPrize();
                        if (this.timer === null) {
                            let tg = (this.raw.length - this.r) * (360 / this.raw.length);
                            this.timer = setInterval(() => {
                                this.counter += 1;
                                this.position += 1;
                                if (this.counter > tg) {
                                    clearInterval(this.timer);
                                    this.timer = null;
                                    this.counter = 0;
                                    this.disabled = false;
                                    setTimeout(()=>{
                                        this.showani = true;
                                        this.showani3 = false
                                        this.showani2 = true;
                                        this.pool.push(this.result);
                                    },2000)
                                }
                            }, 6);
                        }
                    }
                }
            }
        },
    },
    mounted() {
        this.raw = Array.from({ length: 34 }, (_, i) => i + 1);
    },
};
</script>
