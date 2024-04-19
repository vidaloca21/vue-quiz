<template>
    <div>
        <div class="title-area">
            <h2>축구선수 인물퀴즈</h2>
        </div>
        <div v-if="isStart" id="content">
            <div class="img-area">
                <img :src="question[i].img" alt="question-image">
            </div>
            <div class="answer-area">
                <h3 v-if="!isDone"><span :class="time < 3 ? 'ft-red' : ''">{{ time-1 }}.{{ milliSecond }}</span> 초 남았습니다.</h3>
                <h3>맞춘 개수: {{ count }}</h3>
                <div v-if="!isDone" class="input-area">
                    <input type="text" name="answer" v-model="answer" @keyup.enter="isAnswer()" autocomplete='off' v-focus/>
                    <button @click="stop">그만하기</button>
                </div>
                <div v-else class="retry-area">
                    <h3 v-if="count < question.length">정답은 {{ question[i].answer }}입니다.</h3>
                    <h3 v-else>퀴즈를 완료하였습니다</h3>
                    <button @click="retry">다시하기</button>
                </div>
            </div>
        </div>
        <div v-else class="info-area">
            <div class="time-area">
                <label for="time-set">시간 설정</label>
                <select name="time-set" v-model="initalTime">
                    <option value="3">3초</option>
                    <option value="5">5초</option>
                    <option value="7">7초</option>
                </select>
            </div>
            <button @click="start">시작하기</button>
        </div>
    </div>
</template>

<script>

export default {
    name: 'App',
    components: {
    },
    data() {
        return {
            question: [
                {id: 0,
                img: 'https://images.chosun.com/resizer/zkdlXygiW2jv6X7PLYT9nxqQ5CU=/616x0/smart/cloudfront-ap-northeast-1.images.arcpublishing.com/chosun/KRXHWLVSVSELST5IXMZCFOO524.jpg',
                answer: ['손흥민']},
                {id: 1,
                img: 'https://cdn.spotvnews.co.kr/news/photo/202404/672984_1028607_4743.jpg',
                answer: ['이강인', '칸진리']},
                {id: 2,
                img: 'https://sports.chosun.com/news/html/2024/04/18/2024041801001385500187761.jpg',
                answer: ['김민재']},
                {id: 3,
                img: 'https://cdn.interfootball.co.kr/news/photo/202404/628482_651475_3522.jpg',
                answer: ['황희찬', '여진구']},
                {id: 4,
                img: 'https://mydaily.co.kr/photos/2024/03/27/2024032715440523440_l.jpg',
                answer: ['홀란드', '축신', '홀란', '엘링홀란', '엘링 홀란']},
                {id: 5,
                img: 'https://dimg.donga.com/wps/NEWS/IMAGE/2020/09/05/102800493.2.jpg',
                answer: ['메시', '신', '고트', 'GOAT']},
                {id: 6,
                img: 'https://cdn.interfootball.co.kr/news/photo/202110/546804_464468_2023.jpg',
                answer: ['음바페', '킬리안 음바페', '킬리언 음바페']},
                {id: 7,
                img: 'https://image.imnews.imbc.com/news/2020/sports/article/__icsFiles/afieldfile/2020/09/09/hh2020090968.jpg',
                answer: ['데브라이너', '데브라위너', '케빈 데브라이너', '케빈데브라이너', '덕배', '김덕배', '케빈 더 브라위너', '더브라위너']},
                {id: 8,
                img: 'https://img0.yna.co.kr/photo/etc/epa/2023/04/17/PEP20230417030501009_P4.jpg',
                answer: ['린가드', '제시린가드', '제시 린가드']},
                {id: 9,
                img: 'https://images.chosun.com/resizer/LKhG6JYyD4UiexmcxuniUfaBKws=/616x0/smart/cloudfront-ap-northeast-1.images.arcpublishing.com/chosun/TAMEF327BSI2QON5LB566UYSTM.jpg',
                answer: ['해리케인', '케인', '해리 케인']},
            ],
            answer: '',
            count: 0,
            i: 0,
            initalTime: 5,
            time: 0,
            milliSecond: 9,
            isDone: false,
            isStart: false,
        }
    },
    watch: {
        initalTime(newVal) {
            this.time = newVal;
        },
        time(newVal) {
            if (newVal == 0) {
                this.isDone = true;
                console.log("시간초과!")
            }
        }
    },
    computed: {},
    methods: {
        isAnswer() {
            if (this.question[this.i].answer.includes(this.answer)) {
                console.log("정답!")
                this.count++;
                this.answer = '';
                this.initTime();
                if (this.i == this.question.length - 1) {
                    this.isDone = true;
                    return;
                } else {
                    this.i++;
                }
            } else {
                console.log("떙!")
                this.answer = '';
                this.isDone = true;
                this.time = 0;
                this.countDown();
            }
        },
        countDown() {
            let timer = setInterval(() => {
                if (this.time > 0) {
                    this.time--;
                }
                else {
                    clearInterval(timer);
                }
            }, 1000)
           let miliSec = setInterval(() => {
                if (this.milliSecond > 0) {
                    this.milliSecond--;
                }
                else {
                    if (this.time > 0) {
                        this.milliSecond = 9;
                    } else {
                        clearInterval(miliSec);
                    }
                }
            }, 100)
        },
        initTime() {
            this.time = this.initalTime;
        },
        start() {
            this.isStart = true;
            this.isDone = false;
            this.countDown();
            this.initTime();
            this.question = this.shuffle(this.question);
        },
        stop() {
            this.isDone = true;
            this.time = 0;
        },
        retry() {
            this.answer = '';
            this.count = 0;
            this.i = 0;
            this.time = 0;
            this.isDone = false;
            this.isStart = false;
        },
        shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                let j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }
    },
    mounted() {
        this.question = this.shuffle(this.question);
    },
    directives: {
        focus: {
            mounted(elm) {
                elm.focus();
            }
        },
    },
}
</script>

<style>
/* 
yellow: #F2EEE5
pink: E5C1C5
purple: C3E2DD
blue: 6ECEDA
*/
body {
    background-color: #F2EEE5;
    margin: 0;
    padding: 0;
}
ul, li, p, h2, h3 {
    margin: 0;
    padding: 0;
}
h2 {
    font-size: 28px;
    line-height: 30px;
    font-weight: bold;
}

#app {
    width: 768px;
    margin: 0 auto;
    background-color: #FFF;
}
.title-area {
    padding: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
}
#content {
    display: grid;
    min-height: 80vh;
}
.info-area {
    min-height: 66vh;
    display: flex;
    flex-direction: column;
    /* justify-content: center; */
    align-items: center;
    gap: 20px;
    font-size: 18px;
    padding-top: 200px;
}
.time-area {
    width: 240px;
    display: flex;
    justify-content: space-between;
    align-items: center;
}
.img-area {
    height: 400px;
    display: flex;
    align-items: center;
    justify-content: center;
}
.img-area img {
    width: 600px;
    object-fit: cover;
}
.answer-area {
    text-align: center;
}
.input-area {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
.answer-area > h3,
.retry-area > h3 {
    margin: 20px 0;
}
button {
    width: 240px;
    height: 60px;
    font-size: 24px;
    font-weight: bold;
    border: none;
    color: #333;
    background-color: #C3E2DD;
    cursor: pointer;
    margin-top: 20px;
}
input[type=text] {
    width: 200px;
    height: 40px;
    font-size: 18px;
}
select[name=time-set] {
    width: 100px;
    height: 40px;
    font-size: 18px;
}
.ft-red {
    color: red;
}
</style>
