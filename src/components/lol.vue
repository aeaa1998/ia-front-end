/* eslint-disable no-unused-vars */
<template>
    <div class="uk-container uk-container-large">
        <h1 class="uk-text-large uk-text-left">Proyecto de inteligencia Artificial</h1>
        <div class="uk-flex">
            <div v-for="card in cards" :key="card.title" class="uk-card uk-border-rounded uk-card-default uk-width-medium uk-padding-small uk-margin-right">
                <div class="uk-text-small">
                    <div class="uk-card-title uk-text-small">{{ card.title }}</div>
                    {{ card.label }}
                </div>
            </div>
        </div>
        <ul uk-tab>
                <li><a href="#">Ping Pong</a></li>
                <li><a href="#">Boliche</a></li>
        </ul>
        <ul class="uk-switcher uk-margin">
            <li class="uk-margin-small uk-flex">
                <video ref="videRef" @timeupdate="() => playing(24)" class="uk-height-max-medium uk-margin-right" src="../assets/pingPong.mp4"  autoplay="false" controls playsinline uk-video></video>
                <div class="uk-card uk-border-rounded uk-card-default uk-width-1-1 uk-height-max-medium uk-overflow-auto">
                    <div class="uk-padding-small uk-text-left" style="max-height: 100%">
                        <div class="uk-card-title ">Datos relevantes</div>
                        <div class="">
                            <div class="uk-text-bold">Mizutani</div>
                            <div class="uk-grid uk-grid-small">
                                <div class="uk-width-1-2" v-for="i in bollData('Mizutani', frames.pingPong)" :key="i.title">
                                    <div class="uk-flex uk-flex-column">
                                        <div class="uk-text-small">{{i.title}}</div>
                                        <div class="uk-text-meta">{{i.value}}</div>
                                    </div>
                                </div>
                            </div>
                            <div class="uk-text-bold">Boll</div>
                            <div class="uk-grid uk-grid-small">
                                <div class="uk-width-1-2" v-for="i in bollData('Boll', frames.pingPong)" :key="i.title">
                                    <div class="uk-flex uk-flex-column">
                                        <div class="uk-text-small">{{i.title}}</div>
                                        <div class="uk-text-meta">{{i.value}}</div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </li>
            <li class="uk-margin-small uk-flex">
                <video ref="videRefBowl" @timeupdate="() => playing(30, 'bowl', 'Ball')" class="uk-height-max-medium uk-margin-right" src="../assets/bowl_1.mp4"  autoplay="false" controls playsinline uk-video></video>
                <div class="uk-card uk-border-rounded uk-card-default uk-width-1-1 uk-height-max-medium uk-overflow-auto">
                    <div class="uk-padding-small uk-text-left" style="max-height: 100%">
                        <div class="uk-card-title ">Datos relevantes</div>
                        <div class="">
                            <div class="uk-text-bold">Bola de boliche lenta</div>
                            <div class="uk-grid uk-grid-small">
                                <div class="uk-width-1-2" v-for="i in bowlData(frames.bowlJson['Ball'], 'Ball', 1)" :key="i.title">
                                    <div class="uk-flex uk-flex-column">
                                        <div class="uk-text-small">{{i.title}}</div>
                                        <div class="uk-text-meta">{{i.value}}</div>
                                    </div>
                                </div>
                            </div>
                        </div>
                        <div class="">
                            <div class="uk-text-bold">Bola de boliche rapida</div>
                            <div class="uk-grid uk-grid-small">
                                <div class="uk-width-1-2" v-for="i in bowlData(frames.bowlJson['Ball'], 'Ball', 2)" :key="i.title">
                                    <div class="uk-flex uk-flex-column">
                                        <div class="uk-text-small">{{i.title}}</div>
                                        <div class="uk-text-meta">{{i.value}}</div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </li>
        </ul>
        <div id="footer" class="uk-margin">
            Augusto Alonso y David Cuellar
        </div>
    </div>
</template>
<script>
import personsTrack from '../results/pp/personsTrack.json'
// import bowlTrack1 from '../results/bowling/bowlTrack1.json'
import bowlTrack_final from '../results/bowling/bowlTrack_final.json'
export default {
    name: "Video",
    data:() => ({
        cards: [
            {title: 'Que consite?', label: 'Proyecto de inteligencia artificial donde mostraremos como se puede utilizar amazon rekognition'},
            {title: 'Que queremos?', label: 'Sacar un 100nazo para descansar y disfrutar unas buenas vacaiones'},
            {title: 'Como procedimos?', label: 'Para el video de ping pong utilizamos Rekognition sin nada mas y nos otorgo los bounding boxes de las personas y pudimos medir asi los cambios, \n Con el boliche utilizamos custom labels y entrenamos un modelo con fotos en s3.'},
        ],
        results: {
            pingPong: null,
            bowling: null
        },
        prevValues: {
            Ball: {
                'Ball-1': [],
                'Ball-2': []
            }
        },
        frames: {
            pingPong: 0,
            bowl: 0,
            bowlJson: {
                'Ball': 0,
                'Ball2': 0,
            }
        } 
    }),
    beforeMount(){
        this.results.pingPong = Object.keys(personsTrack).reduce((carry, key) => {
            carry[key] = personsTrack[key].reduce((carryBoy, instance) => {
                carryBoy[instance['Frame']] = instance
                return carryBoy
            }, {})
            return carry
        },{})
        // this.results.bowling 
        // = Object.keys(bowlTrack1).reduce((carry, key) => {
        //     carry[key] = bowlTrack1[key].reduce((carryBoy, instance) => {
        //         carryBoy[instance['Frame']] = instance
        //         return carryBoy
        //     }, {})
        //     return carry
        // },{})
        // let r 
       this.results.bowling = Object.keys(bowlTrack_final).reduce((carry, key) => {
            carry[key] = bowlTrack_final[key].reduce((carryBoy, instance) => {
                let i  = 0
                let c = instance.reduce((carry, ball) => {
                    if (!(`Bola-${i+1}` in carry)){
                    carry[`Bola-${i+1}`] = {}
                }
                    carry[`Bola-${i+1}`][`${ball['Frame']}`] =  ball
                    i+=1
                    return carry
                }, carryBoy)
                delete c['Bola-3']
                return c
            }, {})
            
            return carry
        },{})
        console.log(this.results.bowling)
    },
    mounted(){
        this.$refs.videRef.playbackRate = 0.5
        this.$refs.videRefBowl.playbackRate = 0.5
    },
    methods:{
        playing(frameRate, who='ping', ball='Ball'){
            if(who == 'ping'){
                this.frames.pingPong = Math.floor(this.$refs.videRef.currentTime * frameRate)
            }else{
                this.frames.bowlJson[ball] = Math.floor(this.$refs.videRefBowl.currentTime * frameRate)
            }
            
        },
        bowlData(_frame, name, number){
            if(_frame == 0){
                return [{
                title:  'No tiene información disponible', 
                value: 'N/A'
            }]
            }
            let frame = _frame
            if(_frame % 4 != 0){
                if(_frame % 4 > 2){
                    frame = _frame + (4 -(_frame % 4))
                }else{
                    frame = _frame - (_frame % 4)
                }
            }
            let movement;
            let failed = false
            try {
                console.log(this.results.bowling, name, `Bola-${number}`)
                movement = this.results.bowling[name][`Bola-${number}`][`${frame}`]['movement']
            } catch {
                failed = true
                console.log("Frame mod: ",frame,"\nFrame original: ", _frame)
            }
            if(failed){
                return this.prevValues[name][`Bola-${number}`]
            //     return [{
            //     title:  'No tiene información disponible', 
            //     value: 'N/A'
            // }]
            }
            // const movement = this.results.bowling['Ball'][`${frame}`]['movement']
            // const y = {
            //     title:  'Movimiento vertical (cm)', 
            //     value: movement['y'].toFixed(2)
            // }
            const x = {
                title:  'Movimiento (cm)', 
                value: movement['x'].toFixed(2)
            }
            // const y_speed = {
            //     title:  'Vel vertical (cm/s)', 
            //     value: movement['y_speed'].toFixed(2)
            // }
            const x_speed = {
                title:  'Vel (cm/s)', 
                value: movement['x_speed'].toFixed(2)
            }
            // const y_speed_avg = {
            //     title:  'Vel vertical promedio (cm/s)', 
            //     value: movement['y_speed_avg'].toFixed(2)
            // }
            // const x_speed_avg = {
            //     title:  'Vel horizontal promedio (cm/s)', 
            //     value: movement['x_speed_avg'].toFixed(2)
            // }
            this.prevValues[name][`Bola-${number}`] = [
                x,x_speed
            ]
            return [
                x,x_speed
            ]
        },
        bollData(who, _frame) {
            if(_frame == 0){
                return [{
                title:  'No tiene información disponible', 
                value: 'N/A'
            }]
            }
            let frame = _frame
            if(_frame % 12 != 0){
                if(_frame % 12 > 6){
                    frame = _frame + (12 - (_frame % 12))
                }else{
                    frame = _frame - (_frame % 12)
                }
            }
            if(frame < 48){
                return [{
                title:  'No tiene información disponible', 
                value: 'N/A'
            }]
            }
            const movement = this.results.pingPong[who][`${frame}`]['movement']
            const y = {
                title:  'Movimiento vertical (cm)', 
                value: movement['y'].toFixed(2)
            }
            const x = {
                title:  'Movimiento horizontal (cm)', 
                value: movement['x'].toFixed(2)
            }
            const y_speed = {
                title:  'Vel vertical (cm/s)', 
                value: movement['y_speed'].toFixed(2)
            }
            const x_speed = {
                title:  'Vel horizontal (cm/s)', 
                value: movement['x_speed'].toFixed(2)
            }
            const y_speed_avg = {
                title:  'Vel vertical promedio (cm/s)', 
                value: movement['y_speed_avg'].toFixed(2)
            }
            const x_speed_avg = {
                title:  'Vel horizontal promedio (cm/s)', 
                value: movement['x_speed_avg'].toFixed(2)
            }
            return [
                y,x,y_speed,x_speed,y_speed_avg,x_speed_avg
            ]
        }
    }
}
</script>
