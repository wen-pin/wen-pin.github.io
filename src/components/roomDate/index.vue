<template>
    <div>
        <div class="banner">
            <h1> {{ chooseRoom.roomTitle }} </h1>
            <span>1~1人·1床·附早餐·衛浴1間·18平方公尺</span>
            <div class="roomData_text1">
                <p>平日（一～四）價格：{{ chooseRoom.normalDayPrice }} / 假日（五〜日）價格：{{ chooseRoom.holidayPrice }}</p>
                <p>入住時間：15:00（最早）/ 19:00（最晚）</p>
                <p>退房時間：10:00</p>
            </div>
            <div class="roomData_text2">
                <ul>
                    <li>單人間僅供一位客人使用。</li>
                    <li>臥室配有單人床和私人浴室。</li>
                    <li> 您需要的一切為您準備：床單和毯子，毛巾，肥皂和洗髮水，吹風機。 </li>
                    <li> 房間裡有AC，當然還有WiFi。</li>
                </ul>
            </div>
            <div class="roomData_icons">
                <div class="roomData_icons_box" v-for="item in 12" :key="item">
                    <img :src="require(`../roomDate/icons/icon${item}.svg`)" alt="">
                    <div class="roomData_icons_box_own">
                        <img src="./icons/icon98.svg" alt="">
                    </div>
                </div>
            </div>
            <div class="roomData_monthly">
                <p class="roomData_monthly_title">空房狀態查詢</p>
                <div class="roomData_monthly_date">
                    <v-date-picker
                        color="green"
                        v-model="range"
                        is-range
                        :columns="$screens({ default: 1, lg: 2 })"
                        is-expanded
                        :min-date='new Date()'
                    />
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    data() {
        return {
            range: new Date(),
            totalRooms: [
                { name: 'standardSingle',roomTitle: 'Standard Single Room', normalDayPrice: 1200, holidayPrice: 1500 },
				{ name: 'classicSingle',roomTitle: 'Classic Single Room', normalDayPrice: 1600, holidayPrice: 1900 },
                { name: 'deluxeSingle',roomTitle: 'Deluxe Single Room', normalDayPrice: 2000, holidayPrice: 2300 },
                { name: 'standardTwin',roomTitle: 'Standard Twin Room', normalDayPrice: 2400, holidayPrice: 2700 },
                { name: 'classicTwin',roomTitle: 'Classic Twin Room', normalDayPrice: 2800, holidayPrice: 3100 },
                { name: 'deluxeTwin',roomTitle: 'Deluxe Twin Room', normalDayPrice: 3200, holidayPrice: 3500 },
            ],
        }
    },
    computed: {
        chooseDaytotal: function (){
            if(!this.range.end || !this.range.start){
                return 0
            }else{
                let i = 0
                let totalDay = []
                let endTime = this.range.end.getTime()
                let startTime = this.range.start.getTime()
                while((endTime-startTime) >= 0){
                    totalDay[i] = startTime
                    startTime += 86400000
                    i +=1
                }return totalDay.length
            }
        },
        distinguishNormalDay: function (){
            if(!this.range.end || !this.range.start){
                return 0
            }else{
                let i = 0
                let date = ''
                let week = ''
                let normalDay = []
                let allWeek = []
                let endTime = this.range.end.getTime()
                let startTime = this.range.start.getTime()
                while((endTime-startTime) >= 0){
                    date = new Date(startTime)
                    week = date.getDay().toString()
                    allWeek[i] = week
                    startTime += 86400000
                    i +=1
                }
                normalDay = allWeek.filter(function(week){
                    return week.match(/[1-4]/)
                })
                return normalDay.length
            }
        },
        distinguishholiday: function (){
            if(!this.range.end || !this.range.start){
                return 0
            }else{
                let i = 0
                let date = ''
                let week = ''
                let holiday = []
                let allWeek = []
                let endTime = this.range.end.getTime()
                let startTime = this.range.start.getTime()
                while((endTime-startTime) >= 0){
                    date = new Date(startTime)
                    week = date.getDay().toString()
                    allWeek[i] = week
                    startTime += 86400000
                    i +=1
                }
                holiday = allWeek.filter(function(week){
                    return week.match(/[0|5|6]/)
                })
                return holiday.length
            }
        },
        chooseDate: function(){
            if(!this.range.end || !this.range.start){
                return 0
            }else{
                let i = 0
                let allDay= []
                let endTime = this.range.end.getTime()
                let startTime = this.range.start.getTime()
                while((endTime-startTime) >= 0){
                    let date = new Date(startTime)
                    let year = date.getFullYear()
                    let month = date.getMonth() + 1
                    let day = date.getDate()
                    allDay[i] = `${year}-${month}-${day}`
                    startTime +=86400000
                    i +=1
                }
                return allDay
            }
        },
        chooseRoom: function(){
            return this.totalRooms.find( (item) => item.name === this.$route.query.name)
        }
    },
    watch: {
        'range.start': function(){
            const vm = this
            const info = {
                day: vm.chooseDaytotal,
                normalDay: vm.distinguishNormalDay,
                holiday: vm.distinguishholiday,
            }
            this.$router.replace({
                query: { name : this.$route.query.name },
                params: {
                    info: JSON.stringify(info),
                },
            });
        },
        'chooseDate': function (val){
            this.$emit('chooseDateEmit', val)
        },
        chooseDaytotal: function(val){
            this.$emit('chooseDaytotalEmit', val)
        },
    }
}
</script>

<style lang="scss" scoped>
@import "./style";
</style>