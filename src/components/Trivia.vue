<template>
    <div class="container triviaDiv">
        <div class="selectDiv animate__animated animate__zoomIn" v-show="showSelectDiv">
            <h1>Not too important facts</h1>
            <div class="selects">
                <select @change="setCat($event)" class="form-select form-select-lg mb-3" name="categories" id="categories">
                    <option value="arts_and_literature">Arts & Literature</option>
                    <option value="film_and_tv">Film & TV</option>
                    <option value="food_and_drink">Food & Drink</option>
                    <option value="general_knoledge" selected>General Knoledge</option>
                    <option value="geography">Geography</option>
                    <option value="history">History</option>
                    <option value="music">Music</option>
                    <option value="science">Science</option>
                    <option value="soceity_and_ulture">Soceity & Culture</option>
                    <option value="sport_and_leisure">Sport & Leisure</option>
                </select>
                <select @change="setDif($event)" class="form-select form-select-lg mb-3" name="difficulty" id="difficulty">
                    <option value="easy">Easy</option>
                    <option value="medium">Medium</option>
                    <option value="hard">Hard</option>
                </select>
                <button @click="loadData()" class="btn btn-light" name="submit">Get question</button>
            </div>
        </div>
        
        
<!-- This is the div for the games. Questions and asnwers -->
             
            <div class="gameBox" v-show="showQuestion">

                    <div class="animate__animated animate__bounceInDown" v-show="showQuestion">
                        <h1>Question: {{ questionNum + 1 }} / {{ maxQuestion }}</h1>
                        <div class="infoDiv">
                            <h2>Score: {{ userScore }}</h2>
                        </div>
                    </div> 

               
                    <div class="questionBox animate__animated animate__bounceInLeft" v-show="showQuestion">
                        {{ question}}
                    </div>

                <div class="progress mt-4 animate__animated animate__bounceInRight" style="height: 20px; width: 100%;" v-show="showQuestion">
                <div class="progress-bar bg-success" role="progressbar" v-bind:style="{'width':timePercent+'%'}" aria-valuenow="100" aria-valuemin="0" aria-valuemax="60"></div>
                </div>

                <div class="answerBox animate__animated animate__bounceInUp" v-show="showQuestion">
                    <div class="asnwer" v-for="(value, key) in questions" :key="key">
                        <input @change="onChange($event)" type="radio" :id="key" name="choose" :value="value" v-model="selected">
                        <label :for="key">{{ key }}</label>
                    </div>

                </div>
                <button @click="checkAnswer()" class="btn btn-success animate__animated animate__bounceIn" v-show="showQuestion">Check answer</button>      
            </div>

<!-- This is the screen for correct or incorrect answers -->
            <div class="asnwerDiv" v-show="showResult">
                <h1 class="animate__animated animate__zoomIn" v-show="showResult">{{ answerMessage }}</h1>
                <button class="btn btn-success" @click="setNextQuestion()">Next question</button>
            </div>

<!-- This is the finial screen -->
            <div class="finalScreen" v-show="showFinal">
                <h1>This is the game result</h1>
                Your final score is: {{ userScore }}
                Correct answers: {{ userScore / 10 }}
                <button @click="newGame()" class="btn btn-success">New game</button>
            </div>
    </div>
</template>

<script>
    import axios from "axios";
    export default{
        name: "TriviaDiv",
    data(){
        return{
            showResult: false,
            showQuestion: false,
            showSelectDiv: true,
            showFinal: false,
            answerMessage: "",
            userScore: 0,
            response: "",
            question: "",
            questions: [],
            questionNum: 0,
            maxQuestion: 10,
            userAnswer: "",
            timeFull: 60,
            timePercent: 100,
            intervall: null,
            isRunning: false,
            selected: false,
            category: "general_knoledge",
            difficulty: "easy",
            url: "https://the-trivia-api.com/api/questions?",
            striped: true
        }
    },
    
    created () {
        
            setInterval(() => {
                if(this.isRunning){
                    this.timeFull --;
                    this.timePercent = Math.floor((this.timeFull / 60) * 100);
                }
            }, 1000)
        
    },

    mounted(){
        console.log("hello");
        
    },
    methods:{
        uncheckAll: function(){
            this.selected = false;
        },

        showVariables(){
            console.log("-------------------*----------------")
            console.log("showResult: "+this.showResult);
            console.log("showQuestion: "+this.showQuestion);
            console.log("showSelectDiv: "+this.showSelectDiv);
            console.log("showFinal: "+this.showFinal);
            console.log("answerMessage: "+this.answerMessage);
            console.log("response: "+this.response);
            console.log("question: "+this.question);
            console.log("questions: "+this.questions);
            console.log("questionNum: "+this.questionNum+1);
            console.log("userAnswer: "+this.userAnswer);
            console.log("Full time: "+this.timeFull);
            console.log("Running: "+this.isRunning);
        },

        initGame(){
            this.url = "https://the-trivia-api.com/api/questions?categories="+ this.category +"&limit="+ this.maxQuestion +"&difficulty="+this.difficulty;
            this.showQuestion = false;
            this.showResult = false;
            this.showFinal = false;
            this.showSelectDiv = true;
            this.userScore = 0;
            this.questionNum = 0;
            this.userAnswer = "";
            this.questions = [];
        },

        async loadData(){
            this.initGame();
            try{
                this.response = await axios.get(this.url);
                console.log(this.response);
                this.getQuestion()

            }catch(e){
                alert("Error: "+e.response.status+"\nMessage: "+e.response.data.error.message);
            }
        },
        getQuestion(){         
            this.questions = [];
            this.userAnswer = "";
            this.showQuestion = true;
            this.showResult = false;
            this.showSelectDiv = false;
            this.showFinal = false;
            this.timeFull = 60;
            this.timePercent = 100;
            this.question = this.response["data"][this.questionNum]["question"];
            this.questions[this.response["data"][this.questionNum]["correctAnswer"]] = "correct";
            this.questions[this.response["data"][this.questionNum]["incorrectAnswers"][0]] = "incorrect";
            this.questions[this.response["data"][this.questionNum]["incorrectAnswers"][1]] = "incorrect";
            this.questions[this.response["data"][this.questionNum]["incorrectAnswers"][2]] = "incorrect";
            this.questions = this.shuffleObject(this.questions)
            this.isRunning = true;
            this.showVariables();
            // this.toggleTimer();
        },



        setCat(event){
            this.category = event.target.value;
        },
        setDif(event){
            this.difficulty = event.target.value;
            
        },

        setNextQuestion(){
            if (this.questionNum + 1 < this.maxQuestion){
                this.questionNum += 1;
                this.getQuestion()
            }else{
                this.showQuestion = false;
                this.showResult = false;
                this.showSelectDiv = false;
                this.showFinal = true;
            }
        },
        
        checkAnswer(){
            this.isRunning = false;
            if(this.userAnswer){
                this.showQuestion = false;
                if(this.userAnswer === "correct"){
                this.answerMessage = "Yes, this is the correct answer!"; 
                this.userScore += 10;
                this.showResult = true;
                }else{
                    this.answerMessage = "Nope, your answer is not correct!"; 
                    this.showResult = true;
                }
            }else{
                console.log("No answer");
            }
            
        },

        onChange(event) {
              this.userAnswer = event.target.value;
        },

        shuffleObject(obj){
                // new obj to return
            let newObj = {};
                // create keys array
            var keys = Object.keys(obj);
                // randomize keys array
                keys.sort(function(){return Math.random()- 0.5;});
                keys.sort(function(){return Math.random()- 0.5;});
            // save in new array
                keys.forEach(function(k) {
                    newObj[k] = obj[k];
            });
            return newObj;
        },

        newGame(){
            this.initGame()
        }
    }
}
</script>

<style>
    .triviaDiv{
        width: 60vw;
        height: 50vh;
        margin: 0 auto;
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .selects{
        width: 100%;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: center;
        gap: 20px;
    }
    .form-select-lg{
        width: 20ch;
    }

    .questionBox{
        font-size: 1.5em;
        font-weight: 500;
    }

    .gameBox{
        width: 100%;
        margin: 0 auto;
    }

    .answerBox{
        width: 90%;
        margin: 0 auto;
        margin-bottom: 2em;
        margin-top: 2em;
        display: flex;
        justify-content:left;
        flex-wrap: wrap;
        gap: 1em;
    }

    .asnwer{
        flex: 1 0 40%;                 /* NEW */
        font-size: 1.3em;
        text-align: start;
        padding-left: 4ch;
    }

    .infoDiv{
        width: 100%;
        display: flex;
        flex-direction: row;
        justify-content: center;
        gap: 3em;
    }

    .left-enter-active,
    .left-leave-active {
        transition: all 500ms ease-in-out;
    }

    .left-enter-from,
    .left-leave-to {
        transform: translateX(-1000px);
        opacity: 0;
    }

    .right-enter-active,
    .right-leave-active {
        transition-delay: 2500ms;
        transition: all 500ms ease-in-out;
    }

    .right-enter-from,
    .right-leave-to {
        transform: translateX(1000px);
        opacity: 0;
    }

</style>