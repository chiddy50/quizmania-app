<template>
    <div class='question'>
        
        <div class="question__box">
            <!-- PopUp -->
            <!-- <div class="navigation"> -->
                <!-- <input type="checkbox" id="navi-toggle" class="navigation__checkbox"> -->
                <label for="navi-toggle" class="navigation__button" @click="test">
                    <span class="navigation__icon">&nbsp;</span>
                </label>
                <div id="menu-box">
                   <span> Difficulty: {{ currentQuestion.difficulty }} </span>
                   <span> Category: {{ currentQuestion.cartegory }} </span>
                </div>
                
            <!-- </div> -->
            <!-- Popup End -->

            <div class="quiz-question">
                <div class="question-text">
                    <h5 class="text-center">
                        {{ currentQuestion.question }}
                    </h5>
                </div>
            </div>

            <div class="quiz-options">
                <ul class="options-group text-center">
                    <li class="single-option" v-for="(answer, index) in shuffledAnswers" 
                    :key="index"
                    @click="selectAnswer(index)"
                    :class="answerClass(index)"
                    >
                        {{ answer }}
                    </li>
                </ul>
            </div>

            <div class="control-buttons">
                <button class="button-main button-submit"
                    @click="submitAnswer"
                    :disabled="selectedIndex === null || answered">
                Submit
                </button>
                <button @click="next($event)" class="button-main button-next">Next</button>
            </div>
        </div>

    </div>
</template>

<script>
import { bus } from '../main'
import _ from 'lodash';
import Swal from 'sweetalert2'

export default {
    name: 'questionBox',
    props: {
        currentQuestion: Object
    },
    data(){
        return {
            selectedIndex: null,
			shuffledAnswers: [],
			correctIndex: null,
            answered: false,
            attempts: 2
        }
    },
    methods: {
        next(event){            
            bus.$emit('next', event);
        },
        
        selectAnswer(index){
			this.selectedIndex = index;
        },

        submitAnswer(){
            let self = this
			let isCorrect = false
			if (this.selectedIndex === this.correctIndex) {
				isCorrect = true
                bus.$emit('increment', isCorrect)
                this.attempts = 2
                Swal.fire({
                    position: 'top-end',
                    icon: 'success',
                    title: 'Correct!',
                    showConfirmButton: false,
                    timer: 1500
                })
                setTimeout(() => {
                    bus.$emit('next', event); 
                }, 1500);               

			}else{
                isCorrect = false
                this.attempts--
                if (this.attempts <= 0) { // If attempts are exausted, return
                    Swal.fire({
                        position: 'top-end',
                        icon: 'error',
                        title: 'Attempts exausted!!',
                        showConfirmButton: false,
                        timer: 1500
                    })
                    bus.$emit('next', event); 
                    bus.$emit('increment', isCorrect)
                    this.attempts = 2
                    return;
                }
                
                Swal.fire({
                    title: 'Wrong',
                    text: 'Try again, you have ' + this.attempts + ' attempts',
                    icon: 'error',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    cancelButtonText: 'Next question',
                    confirmButtonText: 'Yes, try again'
                    }).then((result) => {
                        // console.log(result);
                    if (result.value) {
                        self.shuffleAnswers()
                        self.selectedIndex = null
                        self.answered = false
                    }
                    else if (result.isDismissed) {
                        self.attempts = 2
                        bus.$emit('next', event);                
                    } 
                })
                
                // console.log(this.attempts);
            }
			this.answered  = true

		},
        
        shuffleAnswers(){
			//we are just making a copy of the incorrect_answers array
			let answers = [...this.currentQuestion.options];
			this.shuffledAnswers = _.shuffle(answers)
			this.correctIndex = this.shuffledAnswers.indexOf(this.currentQuestion.correct_answer)
        },

        answerClass(index){	
			let answerClass = '';
			if (!this.answered && this.selectedIndex === index) {
				answerClass = 'selected'
			}else if (this.answered && this.correctIndex === index) {
				answerClass = 'correct'
			}else if (this.answered && this.selectedIndex === index && this.correctIndex !== index) {
				answerClass = 'incorrect'
			}
			return answerClass;
		},
        
        test(){
            if (document.querySelector("#menu-box").classList.toggle("no-width")) {
                document.querySelector("#menu-box").classList.add("add-width")
                document.querySelector("#menu-box").classList.remove("add-width")

            }else{
                document.querySelector("#menu-box").classList.remove("add-width")
                document.querySelector("#menu-box").classList.add("add-width")

            }
        },
        
    },

    watch:{
		currentQuestion:{
			immediate: true,
			handler(){
				this.selectedIndex = null,
				this.shuffleAnswers(),
				this.answered = false
			}
		}
	},
}
</script>

<style scoped lang="scss">

.question__box{
    position: relative;
    background-color: #fff;
    margin-top: 2rem;
    width: 30%;
    margin: auto;
    padding: 3rem;
    transition: all .8s ease;
    border-radius: .5rem;
    box-shadow: 0 1.5rem 4rem rgba(0, 0, 0, 0.3);
    margin-top: 2rem;
    
    @media (max-width: 768px){
        width: 50%;
    }
    
    @media (max-width: 660px){
        width: 90%;
    }
}





.quiz-question{
    height: 150px;
    box-shadow: 0 1.5rem 4rem rgba(0, 0, 0, 0.3);
    background-image: linear-gradient(to right bottom, #707376, #353536);
    position: relative;
    border-radius: 5px;
    margin: 4rem 0;

    @media (max-width: 600px) {
        height: 120px;
    }
}

.quiz-options{
    height: 200px;
    /* border: 1px dashed ; */
}

.options-group{
    display: flex;
    flex-direction: column;
}

.single-option{
    list-style: none;
    display: block;
    margin: .3rem 0;
    padding: .7rem;
    border-radius: 1.7rem;
    box-shadow: 0 1.5rem 4rem rgba(0, 0, 0, 0.3);
    font-size: 1.5rem;
    transition: all .2s;
    cursor: pointer;

    @media (max-width: 600px) {
        font-size: 2.2rem;
    }
}

.single-option:hover {
    background-image: linear-gradient(to right bottom, #ffb900, #ff7730); 
    color: #fff;
}


.question-text {
    /* border: 1px dashed #fff; */
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: fit-content;
}

.question-text h5{
    font-size: 1.7rem;
    color: #eee;

    @media (max-width: 600px) {
        font-size: 2.2rem;
    }
}

.control-buttons {
    margin-top: 2rem;
    display: flex;
}

.button-main, 
.button-main:link,
.button-main:visited {
    padding: 1rem 3rem;
    color: #fff;
    border-radius: 10rem;
    text-transform: uppercase;
    display: inline-block;
    font-size: 1.6rem;
    border: none;
    cursor: pointer;
    flex: 1;
}

.button-main:focus,
.button-main:active {
    outline: none;
}
.button-next {
    /* background: rgb(197, 37, 37); */
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
    transition: all 0.4s;
    background-image: linear-gradient(to right bottom, #707376, #353536);
}

.button-main:hover {
    box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.4);
}

.button-next:hover {
    background-image: linear-gradient(to right bottom, #ffb900, #ff7730); 
}

.button-submit{
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    transition: all 0.4s;
    background-image: linear-gradient(to right bottom, #ffb900, #ff7730); 
}

.button-submit:hover {
    background-image: linear-gradient(to right bottom, #707376, #353536);
}



#menu-box{ 
    background-image: linear-gradient(to right bottom, #ffb900, #ff7730); 
    height: 40px;
    position: absolute;
    top: 1rem;
    left: 3rem;
    border-radius: 1rem;
    display: flex;
    color: #fff;
    align-items: center;
    font-size: 1.2rem;
    justify-content: space-evenly;
    transition: all 0.6s;
    width: 80%;
    
}

.no-width {
    width: 0 !important;
    opacity: 0;
}

.add-width {
    width: 80% !important;
    opacity: 1;
    transform: scale(1);
    // animation-name: moveInLeft;

}

// @keyframes moveInLeft {
//   0% {
//     transform: translateX(100rem); }
//   80% {
//     transform: translateX(100rem); }
//   100% {
//     transform: translate(0); 
//     opacity: 0;
//     } 
// }





    
.navigation__button {
    position: absolute; 
    // background-image: linear-gradient(to right bottom, #ffb900, #ff7730); 
    background-image: linear-gradient(to right bottom, #707376, #353536);
    height: 3rem;
    width: 3rem;
    top: 1rem;
    right: 1rem;
    border-radius: 50%;
    z-index: 2000;
    box-shadow: 0 1rem 3rem rgba(0, 0, 0, 0.4);
    cursor: pointer; 

    @media (max-width: 56.25em) {
        top: 1rem;
        right: 1rem;  
    }

    @media (max-width: 37.5em) {
        top: 1rem;
        right: 1rem; 
    }
}

    

.navigation__icon {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%); 
}

.navigation__icon, 
.navigation__icon::before, 
.navigation__icon::after {
    width: 1.5rem;
    height: 2px;
    background-color: #fff;
    display: inline-block; 
}

.navigation__icon::after, 
.navigation__icon::before {
    content: "";
    position: absolute;
    left: 0;
    transition: all .4s;
}

.navigation__icon::before {
    top: -0.5rem; 
}

.navigation__icon::after {
    top: 0.5rem; 
}




.correct{
    background: rgb(100, 255, 100);
    cursor:no-drop;
}

.selected{
    background: rgb(150,150,255);
}

.incorrect{
    background: #ff4444;
    cursor:no-drop;
}

.correct:hover{
    background: rgb(100, 255, 100);
    cursor:no-drop;
}
.incorrect:hover{
    cursor:no-drop;
    background: #ff4444;
}







</style>