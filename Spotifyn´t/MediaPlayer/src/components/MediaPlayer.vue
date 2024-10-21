<script lang="ts">
</script>

<template>
    <div id="app">
        <div class="container">
            <div class="main">
                <div class="header">
                    <h1>Spotifyn't</h1>
                </div>
                <div class="song-info">
                    <img
                    v-if="currentSong"
                    :src="currentSong.value.image"
                    class="song-img"
                    />
                    <h2>{{ currentSong ? currentSong.value.title : 'No Song' }}</h2>
                    <h3>{{ currentSong ? currentSong.value.artist : 'No Artist' }}</h3>
                </div>


                <div class="song-controls">
                    <button class="prev">
                        <span @click="prev" class="bx--skip-previous"></span>
                    </button>
                    <button class="play">
                        <span @click="play" class="bx--play"></span>
                    </button>
                    <button @click="next" class="next">
                        <span class="bx--skip-next"></span>
                    </button>
                </div>
                <div class="next-up">
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="css">
    @import url('https://fonts.googleapis.com/css2?family=Meow+Script&display=swap');

    *{
        padding: 0;
        margin: 0;
        box-sizing: border-box;
    }

    body{
        font-family: 'Meow Script', cursive;
        background-color: #965fd4;
    }

    .main{
        width: 500px;
        max-width: 100%;
        margin: 30px auto;
        padding: 30px;

        border-radius: 20px;
        background-color: #734f9a;
        border: 5px solid #8bd450;
        box-shadow: 0px 3px 12px #3f6d4e;
    }

    .header{
        font-family: 'Meow Script', cursive;
        text-align: center;
        font-size: 2rem;
        margin-bottom: 30px;
    }

    .song-img {
        display: block;
        width: 250px;
        max-width: 100%;
        background: red;
        position: relative;

        margin: auto;
        margin-bottom: 30px;
        border-radius: 50%;
        box-shadow: 0px 3px 12px #3f6d4e;
        background-image: url('../assets/BG1.jpg');
        background-position: center;
        background-repeat: no-repeat;
        background-size: cover;

        animation: rotate 10s linear infinite;
    }

    .song-img:hover {
        animation-play-state: paused;
    }

    .song-img::after {
        content: '';
        display: block;
        padding-top: 100%;
    }

    .song-img::before {
        content: '';
        display: block;
        position: absolute;

        top: -10px;
        left: -10px;
        right: -10px;
        bottom: -10px;

        border: 3px solid #8bd450;
        border-radius: 50%;
        box-shadow: 0px 3px 15px #3f6d4e;
    }

    @keyframes rotate {
        from {
            transform: rotate(0deg);
        }

        to {
            transform: rotate(360deg);
        }
    }


    .song-info{
        text-align: center;
        margin-bottom: 30px; 
    }

    .song-info h2{
        font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
        font-weight: 800;
    }

    .song-info h3{
        font-family: 'Courier New', Courier, monospace;
        font-style: italic;
        font-weight: 500;
    }

    button{
        appearance: none;
        border:none;
        outline: none;
        background: none;
    }
    
    .song-controls{
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .bx--play { 
        display: inline-block;
        width: 80px;
        height: 80px;
        background-repeat: no-repeat;
        background-size: 100% 100%;
        background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='M7 6v12l10-6z'/%3E%3C/svg%3E");
        transition: filter 0.3s;
    }

    .bx--play:active {
        filter: brightness(0.5);
    }

    .bx--skip-previous {
        display: inline-block;
        width: 50px;
        height: 50px;
        background-repeat: no-repeat;
        background-size: 100% 100%;
        background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='m16 7l-7 5l7 5zm-7 5V7H7v10h2z'/%3E%3C/svg%3E");
        transition: filter 0.3s;
    }

    .bx--skip-previous:active {
        filter: brightness(0.5);
    }

    .bx--skip-next {
        display: inline-block;
        width: 50px;
        height: 50px;
        background-repeat: no-repeat;
        background-size: 100% 100%;
        background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='M7 7v10l7-5zm9 10V7h-2v10z'/%3E%3C/svg%3E");
        transition: filter 0.3s;
    }

    .bx--skip-next:active {
        filter: brightness(0.5);
    }

</style>