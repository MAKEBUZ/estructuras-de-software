<script lang="ts">
import song1 from './assets/song1.mp3';
import song1Image from './assets/song1.jpg';
import song2 from './assets/song2.mp3';
import song2Image from './assets/song2.jpg';
import song3 from './assets/song3.mp3';
import song3Image from './assets/song3.jpg';
import song4 from './assets/song4.mp3';
import song4Image from './assets/song4.jpg';
import song5 from './assets/song5.mp3';
import song5Image from './assets/song5.jpg';
import song6 from './assets/song6.mp3';
import song6Image from './assets/song6.jpg';
import song7 from './assets/song7.mp3';
import song7Image from './assets/song7.jpg';
import song8 from './assets/song8.mp3';
import song8Image from './assets/song8.jpg';

class Node {
  value: any;
  next: Node | null;
  prev: Node | null;

  constructor(value: any) {
    this.value = value;
    this.next = null;
    this.prev = null;
  }
}

class DoublyLinkedList {
  head: Node | null;
  tail: Node | null;
  length: number;

  constructor() {
    this.head = null;
    this.tail = null;
    this.length = 0;
  }

  append(value: any) {
    const newNode = new Node(value);
    if (this.head === null) {
      this.head = newNode;
      this.tail = newNode;
    } else {
      newNode.prev = this.tail;
      this.tail!.next = newNode;
      this.tail = newNode;
    }
    this.length++;
  }

  prev(node: Node | null): Node | null {
    return node ? node.prev : null;
  }

  next(node: Node | null): Node | null {
    return node ? node.next : null;
  }
}

export default {
  name: 'app',
  data() {
    return {
      currentSong: {
        image: '',
        title: '',
        artist: '',
        src: ''
      },
      isPlaying: false,
      progress: 0,
      songs: [
        {
          title: 'Grateful',
          artist: 'Neffex',
          src: song1,
          image: song1Image
        },
        {
          title: 'Invincible',
          artist: 'Deaf Kev',
          src: song2,
          image: song2Image
        },
        {
          title: 'Onegay Muscle',
          artist: 'Eurobeat Remix',
          src: song3,
          image: song3Image
        },
        {
          title: 'Pizza Rolls',
          artist: 'Shawn Wasabi',
          src: song4,
          image: song4Image
        },
        {
          title: 'Symbolism',
          artist: 'Electrolight',
          src: song5,
          image: song5Image
        },
        {
          title: 'Cartoon',
          artist: 'Daniel Levi',
          src: song6,
          image: song6Image
        },
        {
          title: 'Feelings',
          artist: 'Facading',
          src: song7,
          image: song7Image
        },
        {
          title: 'Lost Sky',
          artist: 'Chris Linton',
          src: song8,
          image: song8Image
        }
      ],
      player: new Audio(),
      volume: 0.5,
      playlist: new DoublyLinkedList()
    };
  },
  created() {
    this.songs.forEach(song => this.playlist.append(song)); // Poblamos la lista de reproducción
    this.currentSong = this.playlist.head ? this.playlist.head.value : {};
    this.player.src = this.currentSong.src;
    this.player.volume = this.volume;
    this.player.addEventListener('ended', this.next);
    this.player.addEventListener('timeupdate', this.updateProgress);
  },
  methods: {
    lowerVolume() {
      if (this.volume > 0) {
        this.volume = Math.max(this.volume - 0.1, 0);
        this.player.volume = this.volume;
      }
    },
    increaseVolume() {
      if (this.volume < 1) {
        this.volume = Math.min(this.volume + 0.1, 1);
        this.player.volume = this.volume;
      }
    },
    play(song: any = null) {
      if (song && song.src !== this.currentSong.src) {
        this.currentSong = song;
        this.player.src = this.currentSong.src;
        this.player.currentTime = 0; // Reinicia el tiempo solo si la canción es diferente
      }
      if (!this.isPlaying) {
        this.player.play();
        this.isPlaying = true;
      }
    },  
    pause() {
      this.player.pause();
      this.isPlaying = false;
    },
    next() {
      const nextSong = this.playlist.next(this.playlist.head);
      if (nextSong) {
        this.playlist.head = nextSong; // Actualiza la cabeza de la playlist
        this.currentSong = nextSong.value;
        this.player.src = this.currentSong.src; // Actualiza la fuente del audio
        this.player.currentTime = 0; // Reinicia el tiempo
        this.player.play(); // Reproduce la siguiente canción
        this.isPlaying = true; // Actualiza el estado de reproducción
      }
    },
    prev() {
      const prevSong = this.playlist.prev(this.playlist.head);
      if (prevSong) {
        this.playlist.head = prevSong; // Actualiza la cabeza de la playlist
        this.currentSong = prevSong.value;
        this.player.src = this.currentSong.src; // Actualiza la fuente del audio
        this.player.currentTime = 0; // Reinicia el tiempo
        this.player.play(); // Reproduce la canción anterior
        this.isPlaying = true; // Actualiza el estado de reproducción
      }
    },
    updateProgress() {
      this.progress = (this.player.currentTime / this.player.duration) * 100 || 0;
    },
    setProgress(event: Event) {
      const target = event.target as HTMLInputElement;
      const time = (Number(target.value) / 100) * this.player.duration;
      this.player.currentTime = time;
    }
  }
};
</script>



<template>
  <div id="app">
    <header>
      <h1>Spotifyn't</h1>
    </header>
    <main>
      <div class="main">
        <section class="player">
          <div class="song-info">
            <img v-if="currentSong.image" :src="currentSong.image" class="song-img" />
            <h2>{{ currentSong.title || 'No Song' }}</h2>
            <h3>{{ currentSong.artist || 'No Artist' }}</h3>
            <input type="range" class="progress-bar" v-model="progress" @input="setProgress" />
          </div>
          <div class="song-controls">
            <button class="prev" @click="prev">
              <span class="bx--skip-previous"></span>
            </button>
            <button class="play" v-if="!isPlaying" @click="play(currentSong)">
              <span class="bx--play"></span>
            </button>
            <button class="pause" v-else @click="pause">
              <span class="bx--pause"></span>
            </button>
            <button class="next" @click="next">
              <span class="bx--skip-next"></span>
            </button>
          </div>
          <div class="volume-controls">
            <button @click="lowerVolume">
              <span class="bx--volume-low"></span>
            </button>
            <button @click="increaseVolume">
              <span class="bx--volume-full"></span>
            </button>
          </div>
        </section>
      </div>
      <section class="playlist">
        <h1 class="title-play">The Playlist</h1>
        <button
          v-for="song in songs"
          :key="song.src"
          @click="play(song)"
          :class="(song.src === currentSong.src) ? 'song playing' : 'song'"
        >
          <img :src="song.image" class="song-img-small" />
          {{ song.title }} - {{ song.artist }}
        </button>
      </section>
    </main>
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

header{
  background-color: #734f9a;
  font-family: 'Meow Script', cursive;
  text-align: center;
  font-size: 2rem;
  margin-bottom: 40px;
  box-shadow: 0px 4px 12px #8bd450;
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

.song-img {
  display: block;
  width: 250px;
  height: 250px;
  background: red;
  position: relative;
  object-fit: cover;

  margin: auto;
  margin-bottom: 30px;
  border-radius: 50%;
  box-shadow: 0px 3px 12px #1d1a2f;
  background-position: center;
  background-repeat: no-repeat;
  background-size: cover;

  animation: rotate 10s linear infinite;
}

.song-img-small {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  margin-right: 10px;
  object-fit: cover;
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
  position: relative;

  top: -20px;
  left: -20px;
  right: -20px;
  bottom: -20px;

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

.bx--pause {
  display: inline-block;
  width: 80px;
  height: 80px;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='M8 7h3v10H8zm5 0h3v10h-3z'/%3E%3C/svg%3E");
}

.volume-controls{
  display: flex;
  justify-content: center;
  align-items: center;
}

.bx--volume-low {
  display: inline-block;
  margin-left: -400px;
  width: 50px;
  height: 50px;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='M4 17h2.697l5.748 3.832a1 1 0 0 0 1.027.05A1 1 0 0 0 14 20V4a1 1 0 0 0-1.554-.832L6.697 7H4c-1.103 0-2 .897-2 2v6c0 1.103.897 2 2 2m0-8h3c.033 0 .061-.016.093-.019a1 1 0 0 0 .379-.116c.026-.014.057-.017.082-.033L12 5.868v12.264l-4.445-2.964c-.025-.018-.056-.02-.082-.033a1 1 0 0 0-.382-.116C7.059 15.016 7.032 15 7 15H4zm12-2v10c1.225-1.1 2-3.229 2-5s-.775-3.9-2-5'/%3E%3C/svg%3E");
  transition: filter 0.3s;
}

.bx--volume-low:active {
  filter: brightness(0.5);
}

.bx--volume-full {
  display: inline-block;
  margin-right: -400px;
  width: 50px;
  height: 50px;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='M16 21c3.527-1.547 5.999-4.909 5.999-9S19.527 4.547 16 3v2c2.387 1.386 3.999 4.047 3.999 7S18.387 17.614 16 19z'/%3E%3Cpath fill='%238bd450' d='M16 7v10c1.225-1.1 2-3.229 2-5s-.775-3.9-2-5M4 17h2.697l5.748 3.832a1 1 0 0 0 1.027.05A1 1 0 0 0 14 20V4a1 1 0 0 0-1.554-.832L6.697 7H4c-1.103 0-2 .897-2 2v6c0 1.103.897 2 2 2m0-8h3c.033 0 .061-.016.093-.019a1 1 0 0 0 .38-.116c.026-.015.057-.017.082-.033L12 5.868v12.264l-4.445-2.964c-.025-.017-.056-.02-.082-.033a1 1 0 0 0-.382-.116C7.059 15.016 7.032 15 7 15H4z'/%3E%3C/svg%3E");
  transition: filter 0.3s;
}

.bx--volume-full:active {
  filter: brightness(0.5);
}

.title-play{
    font-family: 'Meow Script', cursive;
    text-align: center;
    font-size: 2rem;
    margin-bottom: 30px;
}

.playlist{
  padding: 0px 30px;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
}
.playlist h3{
  color: black;
  font-size: 25px;
  font-weight: 400;
  margin-bottom: 30px;
  text-align: center;
}
.playlist .song{
  display: flex;
  align-items: center;
  width: 100%;
  padding: 15px;
  font-size: 20px;
  font-weight: 700;
  cursor: pointer;
}
.playlist .song:hover{
  color: #3f6d4e;
}
.playlist .song.playing {
  box-shadow: 0px 3px 12px #3f6d4e;
  border-radius: 20px;
  border: 2px solid #8bd450;
  color: #FFF;
  background: #734f9a;
}

.progress-bar {
  width: 100%;
  height: 10px;
  background-color: #ccc;
  border-radius: 5px;
  overflow: hidden;
  margin-bottom: 20px;
}

input[type="range"] {
  -webkit-appearance: none;
  width: 100%;
  height: 10px;
  background: #ccc;
  border-radius: 5px;
  cursor: pointer;
  margin-bottom: 20px;
}

input[type="range"]::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #8bd450;
  cursor: pointer;
}

input[type="range"]::-moz-range-thumb {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #8bd450;
  cursor: pointer;
}

</style>