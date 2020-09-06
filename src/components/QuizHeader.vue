<template>
    <div>
        <nav class="navbar navbar-expand-sm navbar-bg text-light mx-auto">
        <ul class="navbar-nav mx-auto">
            <li class="nav-item mx-5">
                <a class="nav-link text-light app-title" disabled><b>CodeArt Quiz App</b></a>
            </li>
            <li class="nav-item text-light mx-5">
                <a class="nav-link text-light counter-text" disabled>
                    Counter: {{ numCorrect }}/{{ numTotal }}
                    <!-- Counter: 0/0 -->
                </a>
            </li>
        </ul>
        </nav>
    </div>
</template>

<script>
import { bus } from '../main'

export default {
    name: 'quizHeader',
    data(){
        return {
            numCorrect: 0,
            numTotal: 0
        }
    },
    
    created(){
        bus.$on('increment', (value) => {
            if (value) {
                this.numCorrect++    
            }
            this.numTotal++
        })
    }
}
</script>

<style scoped>
.navbar-bg {
    background-image: linear-gradient(to right bottom, #707376, #353536);
}

@media (max-width: 600px) {
    .app-title,
    .counter-text{
        font-size: 2.5rem;
        text-align: center;
    }
}
</style>