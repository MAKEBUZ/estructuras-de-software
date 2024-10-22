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
import song9 from './assets/song9.mp3';
import song9Image from './assets/song9.jpg';
import song10 from './assets/song10.mp3';
import song10Image from './assets/song10.jpg';
import song11 from './assets/song11.mp3';
import song11Image from './assets/song11.jpg';
import song12 from './assets/song12.mp3';
import song12Image from './assets/song12.jpg';

class MediaPlayer {
  value: any;
  next: MediaPlayer | null;
  prev: MediaPlayer | null;

  constructor(value: any) {
    this.value = value;
    this.next = null;
    this.prev = null;
  }
}

class MusicManager {
  head: MediaPlayer | null;
  tail: MediaPlayer | null;
  length: number;

  constructor() {
    this.head = null;
    this.tail = null;
    this.length = 0;
  }

  append(value: any) {
    const newMediaPlayer = new MediaPlayer(value);
    if (this.head === null) {
      this.head = newMediaPlayer;
      this.tail = newMediaPlayer;
    } else {
      newMediaPlayer.prev = this.tail;
      this.tail!.next = newMediaPlayer;
      this.tail = newMediaPlayer;
    }
    this.length++;
  }

  remove(MediaPlayer: MediaPlayer) {
    if (MediaPlayer.prev) MediaPlayer.prev.next = MediaPlayer.next;
    if (MediaPlayer.next) MediaPlayer.next.prev = MediaPlayer.prev;
    if (MediaPlayer === this.head) this.head = MediaPlayer.next;
    if (MediaPlayer === this.tail) this.tail = MediaPlayer.prev;
    this.length--;
  }

  moveUp(MediaPlayer: MediaPlayer) {
    if (MediaPlayer.prev) {
      const prevMediaPlayer = MediaPlayer.prev;
      const nextMediaPlayer = MediaPlayer.next;

      if (prevMediaPlayer.prev) prevMediaPlayer.prev.next = MediaPlayer;
      MediaPlayer.prev = prevMediaPlayer.prev;
      MediaPlayer.next = prevMediaPlayer;
      prevMediaPlayer.prev = MediaPlayer;
      prevMediaPlayer.next = nextMediaPlayer;
      if (nextMediaPlayer) nextMediaPlayer.prev = prevMediaPlayer;

      if (MediaPlayer === this.tail) this.tail = prevMediaPlayer;
      if (prevMediaPlayer === this.head) this.head = MediaPlayer;
    }
  }

  moveDown(MediaPlayer: MediaPlayer) {
    if (MediaPlayer.next) {
      const nextMediaPlayer = MediaPlayer.next;
      const prevMediaPlayer = MediaPlayer.prev;

      if (nextMediaPlayer.next) nextMediaPlayer.next.prev = MediaPlayer;
      MediaPlayer.next = nextMediaPlayer.next;
      MediaPlayer.prev = nextMediaPlayer;
      nextMediaPlayer.next = MediaPlayer;
      nextMediaPlayer.prev = prevMediaPlayer;
      if (prevMediaPlayer) prevMediaPlayer.next = nextMediaPlayer;

      if (MediaPlayer === this.head) this.head = nextMediaPlayer;
      if (nextMediaPlayer === this.tail) this.tail = MediaPlayer;
    }
  }

  prev(MediaPlayer: MediaPlayer | null): MediaPlayer | null {
    return MediaPlayer ? MediaPlayer.prev : null;
  }

  next(MediaPlayer: MediaPlayer | null): MediaPlayer | null {
    return MediaPlayer ? MediaPlayer.next : null;
  }

  toArray(): any[] {
    const result = [];
    let current = this.head;
    while (current) {
      result.push(current.value);
      current = current.next;
    }
    return result;
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
          title: '初夏 (Shoka)',
          artist: '【Ado】',
          src: song1,
          image: song1Image
        },
        {
          title: 'ルル (RuLe)',
          artist: '【Ado】',
          src: song2,
          image: song2Image
        },
        {
          title: 'The Nights',
          artist: 'Avicii',
          src: song3,
          image: song3Image
        },
        {
          title: 'Walk Thru Fire',
          artist: 'Vicetone (feat. Meron Ryan)',
          src: song4,
          image: song4Image
        },
        {
          title: 'Nevada',
          artist: 'Vicetone (feat. Cozi Zuehlsdorff)',
          src: song5,
          image: song5Image
        },
        {
          title: '最果てへ',
          artist: 'Reche',
          src: song6,
          image: song6Image
        },
        {
          title: 'BALALAIKA',
          artist: '9Lana',
          src: song7,
          image: song7Image
        },
        {
          title: 'This Will Be The Day',
          artist: 'Jeff Williams (feat. Casey Lee Williams)',
          src: song8,
          image: song8Image
        },
        {
          title: 'Inferno',
          artist: 'Mrs. GREEN APPLE',
          src: song9,
          image: song9Image
        },
        {
          title: 'La Da Dee',
          artist: 'Cody Simpson',
          src: song10,
          image: song10Image
        },
        {
          title: 'クラクラ',
          artist: '【Ado】',
          src: song11,
          image: song11Image
        },
        {
          title: 'Nightcore Go Go Go',
          artist: 'Felsenstein',
          src: song12,
          image: song12Image
        }
      ],
      player: new Audio(),
      volume: 0.5,
      playlist: new MusicManager()
    };
  },
  created() {
    this.songs.forEach(song => this.playlist.append(song));
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
        this.player.currentTime = 0;
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
        this.playlist.head = nextSong;
        this.currentSong = nextSong.value;
        this.player.src = this.currentSong.src;
        this.player.currentTime = 0;
        this.player.play();
        this.isPlaying = true;
      }
    },
    prev() {
      const prevSong = this.playlist.prev(this.playlist.head);
      if (prevSong) {
        this.playlist.head = prevSong;
        this.currentSong = prevSong.value;
        this.player.src = this.currentSong.src;
        this.player.currentTime = 0;
        this.player.play();
        this.isPlaying = true;
      }
    },
    updateProgress() {
      this.progress = (this.player.currentTime / this.player.duration) * 100 || 0;
    },
    setProgress(event: Event) {
      const target = event.target as HTMLInputElement;
      const time = (Number(target.value) / 100) * this.player.duration;
      this.player.currentTime = time;
    },
    removeSong(song: any) {
      let MediaPlayer = this.playlist.head;
      while (MediaPlayer) {
        if (MediaPlayer.value.src === song.src) {
          this.playlist.remove(MediaPlayer);
          this.songs = this.playlist.toArray();
          if (this.currentSong.src === song.src) {
            this.next();
          }
          break;
        }
        MediaPlayer = MediaPlayer.next;
      }
    },
    moveSongUp(song: any) {
      let MediaPlayer = this.playlist.head;
      while (MediaPlayer) {
        if (MediaPlayer.value.src === song.src) {
          this.playlist.moveUp(MediaPlayer);
          this.songs = this.playlist.toArray();
          break;
        }
        MediaPlayer = MediaPlayer.next;
      }
    },
    moveSongDown(song: any) {
      let MediaPlayer = this.playlist.head;
      while (MediaPlayer) {
        if (MediaPlayer.value.src === song.src) {
          this.playlist.moveDown(MediaPlayer);
          this.songs = this.playlist.toArray();
          break;
        }
        MediaPlayer = MediaPlayer.next;
      }
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
        <div
          v-for="song in songs"
          :key="song.src"
          @click="play(song)"
          :class="(song.src === currentSong.src) ? 'song playing' : 'song'">
          <img :src="song.image" class="song-img-small" />
          {{ song.title }} - {{ song.artist }}
          <div class="song-actions">
            <button @click.stop="removeSong(song)">
              <span class="bx--trash"></span>
            </button>
            <button @click.stop="moveSongUp(song)">
              <span class="bx--caret-up"></span>
            </button>
            <button @click.stop="moveSongDown(song)">
              <span class="bx--caret-down"></span>
            </button>
          </div>
        </div>
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
  color: #8bd450;
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
  background-image: url('./assets/eva01-bg3.png');
  background-repeat: no-repeat;
  background-size: cover;
  background-position: center center;
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
  color: #8bd450;
    font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
    font-weight: 800;
}

.song-info h3{
  color: #8bd450;
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

.playlist {
  position: relative; 
  padding: 0px 30px;
  font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif;
  border: 3px solid #8bd450;
  box-shadow: 0px 3px 15px #3f6d4e;
}

.playlist::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-image: url('./assets/eva01.png');
  background-repeat: no-repeat;
  background-size: cover;
  background-position: bottom center;
  opacity: 0.3;
  z-index: -1; 
}

.playlist h1 {
  color: #8bd450;
  font-family: 'Meow Script', cursive;
  text-align: center;
  font-size: 2rem;
}

.playlist .song {
  color: #8bd450;
  display: flex;
  align-items: center;
  width: 100%;
  padding: 15px;
  font-size: 20px;
  font-weight: 700;
  cursor: pointer;
}

.playlist .song:hover {
  color: #3f6d4e;
}

.playlist .song.playing {
  box-shadow: 0px 3px 12px #3f6d4e;
  border-radius: 20px;
  border: 2px solid #8bd450;
  color: #FFF;
  background: #734f9a;
}

.bx--trash {
  text-align: right;
  display: inline-block;
  width: 50px;
  height: 50px;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='M5 20a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2V8h2V6h-4V4a2 2 0 0 0-2-2H9a2 2 0 0 0-2 2v2H3v2h2zM9 4h6v2H9zM8 8h9v12H7V8z'/%3E%3Cpath fill='%238bd450' d='M9 10h2v8H9zm4 0h2v8h-2z'/%3E%3C/svg%3E");
}

.bx--caret-down {
  display: inline-block;
  width: 50px;
  height: 50px;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='m11.998 17l7-8h-14z'/%3E%3C/svg%3E");
}

.bx--caret-up {
  display: inline-block;
  width: 50px;
  height: 50px;
  background-repeat: no-repeat;
  background-size: 100% 100%;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%238bd450' d='M5 15h14l-7-8z'/%3E%3C/svg%3E");
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