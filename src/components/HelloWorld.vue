<template>
  <div class="container">
    <div class="applogo">
      <a href>
        <div class="logo">
          <img src alt />
        </div>
      </a>
    </div>
    <div col-md-12>
      <div class="addcol">
        <div class="float-right">
          <button class="btn">+Add</button>
          <input
            type="file"
            name="files"
            id="file"
            ref="myFiles"
            @change="previewFiles()"
            class="upload-btn"
            accept="audio/*"
            multiple
          />
        </div>
      </div>
    </div>

    <h3>Music List</h3>
    <div class="col-md-4">
      <div class="scroll">
        <div v-for="(item, index) in musicFiles" :key="index">
          <div
            class="musics"
            :class="selectedMusic.index == index ? 'active' : ''"
            @click="selectMusic(item.url,item.name, index)"
          >
            <div class="song">
              {{ item.name.split('.')[0].substr(0,10) }}
              <audio
                class="permusic"
                :src="item.url"
                controls
                style="display:none"
              ></audio>
            </div>
            <div class="play" id="play-pause">
              <li :class="selectedMusic.index == index ? 'fa fa-pause' : 'fa fa-play'"></li>
            </div>
            <div class="time">{{ time[index] || null }}</div>
          </div>
        </div>
        <!-- <div class="music-list">
                    <span><img src="image/play.png" alt="" width="30px" height="30px"></span>
                    <marquee>Ayefele Songahhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh</marquee>
        </div>-->
      </div>
    </div>
    <div class="col-md-6">
      <div class="displaytv">
        <div class="boxtv">
          <div class="col-md-12">
            <div class="music-name">
              <marquee behavior="alternate" direction="up">{{ (selectedMusic.name || null) }}</marquee>
              <audio ref="audiomusic" :src="selectedMusic.url"></audio>
            </div>
          </div>
          <div class="col-md-12">
            <div class="screen">
              <li class="fa fa-headphones fa-5x"></li>
            </div>
          </div>
          <input
            type="range"
            min="0"
            max="100"
            style="width:100%"
            v-model="seek"
            @change="seeking"
            step="1"
          />
          <div class="col-md-12">
            <div class="volume">
              <div class="volrange">
                <input type="range" min="0" max="100" v-model="vol" @change="reduceIncreaseVolume" />
              </div>
              <div class="speaker">
                <li
                  :class="{'fa fa-volume-up fa-2x' : volumemute , 'fa fa-volume-off fa-2x' : !volumemute}"
                  @click="muteUnmute"
                ></li>
              </div>
            </div>
          </div>
          <div class="col-md-12">
            <div class="controls">
              <div class="pre-btn" @click="backWard(selectedMusic.index)">
                <li class="fa fa-backward fa-2x"></li>
              </div>
              <div class="playbtn" @click="playOrPauseMusic">
                <!-- <li v-if="isPlaying == false" class="fas fa-play fa-2x"></li> -->
                <!-- <li v-else class="fas fa-pause fa-2x"></li> -->

                <li :class="isPlaying == false ? 'fa fa-pause fa-2x' : 'fa fa-play fa-2x'"></li>
              </div>
              <div class="nxt-btn">
                <li class="fa fa-forward fa-2x" @click="forWard(selectedMusic.index)"></li>
              </div>
            </div>
          </div>
          <div class="col-md-12">
            <div class="functions">
              <div class="repeat">
                <li class="fa fa-sync fa-2x"></li>
              </div>
              <div class="shuffle">
                <li class="fa fa-random fa-2x"></li>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data() {
    return {
      isPlaying: true,
      volumemute: true,
      vol: 30,
      files: [],
      musicFiles: [],
      selectedMusic: {},
      currentSong: 0,
      time: [],
      seek: 0,
    };
  },
  created() {
    // this.onMusicPlaying();
  },
  watch: {
    // isPlaying() {
    // //   while (this.isPlaying == false) {
    // //     this.onMusicPlaying();
    // //   }
    // }
    // seek(){
    //   this.onMusicPlaying();
    // }
  },
  methods: {
    previewFiles() {
      this.files = this.$refs.myFiles.files;
      if (this.$refs.myFiles.files.length < 1) return false;

      for (let index = 0; index < this.$refs.myFiles.files.length; index++) {
        var url = URL.createObjectURL(this.$refs.myFiles.files[index]);

        const musicList = {
          name: this.$refs.myFiles.files[index].name,
          url: url,
        };
        // var audio = document.createElement('audio');
        // audio.src=url;
        // // console.log(audio.duration);
        // console.log(audio);

        this.musicFiles.push(musicList);
      }

      this.musicDuration();
    },
    selectMusic(url, name, index) {
      this.selectedMusic = {
        index: index,
        name: name.split(".")[0],
        url: url,
      };
      var vd = this;
      setTimeout(() => {
        vd.$refs.audiomusic.play();
        vd.isPlaying = false;
      this.onMusicPlaying();
        
      }, 1000);

      this.currentSong = index;
      console.log("current", this.currentSong);
      console.log(
        "Music selected",
        this.selectedMusic,
        this.selectedMusic.name
      );
    },
    playOrPauseMusic() {
      // console.log(Math.floor(this.$refs.audiomusic.currentTime))
      if (this.$refs.audiomusic.paused) {
        this.$refs.audiomusic.play();
        console.log("Playing....");
        this.isPlaying = false;
      } else {
        this.$refs.audiomusic.pause();
        console.log("Pausing...");
        this.isPlaying = true;
      }
    },
    backWard(index) {
      var newIndex = --index;
      newIndex = newIndex < 0 ? 0 : newIndex;
      var findMusic = this.musicFiles[newIndex];
      console.log(findMusic);
      if (findMusic != null || findMusic != undefined) {
        this.selectMusic(
          this.musicFiles[newIndex].url,
          this.musicFiles[newIndex].name,
          newIndex
        );
      }
    },
    forWard(index) {
      var newIndex = ++index;
      //  console.log("arr len",this.musicFiles.length);
      newIndex = newIndex > this.musicFiles.length - 1 ? 0 : newIndex;
      console.log(newIndex);
      var findMusic = this.musicFiles[newIndex];
      console.log(findMusic);
      if (findMusic != null || findMusic != undefined) {
        this.selectMusic(
          this.musicFiles[newIndex].url,
          this.musicFiles[newIndex].name,
          newIndex
        );
      }
    },
    musicDuration() {
      let vm = this;
      setTimeout(() => {
        let musics = document.querySelectorAll(".permusic");
        musics.forEach((element) => {
          vm.time.push(this.convertSecToMin(element.duration));
        });
        console.log(vm.time);
      }, 1000);
    },
    convertSecToMin(timeinseconds) {
      let result = "";
      var mins = Math.floor(timeinseconds / 60);
      let secs = Math.floor(timeinseconds - mins * 60);
      if (mins >= 60) {
        var hours = Math.floor(mins / 60);
        let minInHr = Math.floor(mins / 60);
        result =
          (hours < 10 ? `0${hours}` : hours) +
          ":" +
          (minInHr < 10 ? `0${minInHr}` : minInHr) +
          ":" +
          (secs < 10 ? `0${secs}` : secs);
      } else {
        result =
          (mins < 10 ? `0${mins}` : mins) +
          ":" +
          (secs < 10 ? `0${secs}` : secs);
      }

      return result;
      // console.log(mins);
      //   var minutes = Math.floor(timeinseconds / 60);
      //   var seconds = Math.floor(timeinseconds - minutes * 60);

      //  if(minutes >= 60 ){
      //     var hours = Math.floor(minutes/60);
      //     var newmin = Math.floor(minutes-hours * 60);
      //     var divnewmin = Math.floor(minutes / 60);
      //     var newsec = Math.floor(minutes-divnewmin * 60);
      //     return hours + ":" + newmin + ":" + newsec;
      //  }else{
      //    return minutes +":"+ seconds;
      //  }
    },
    muteUnmute() {
      if (this.$refs.audiomusic.muted) {
        this.$refs.audiomusic.muted = false;
        this.volumemute = true;
      } else {
        this.$refs.audiomusic.muted = true;
        this.volumemute = false;
      }
    },
    reduceIncreaseVolume() {
      this.$refs.audiomusic.volume = parseFloat(this.vol / 100);
      console.log(parseFloat(this.vol / 100));
    },
    onMusicPlaying() {
      var vn = this;
      var setIn = setInterval(() => {

        var duration = vn.$refs.audiomusic.duration;
        let currentTime = vn.$refs.audiomusic.currentTime;
        console.log(duration, currentTime);
        vn.seek = (currentTime / duration) * 100; 
      if (vn.seek==100){
        setIn.clearInterval();
        vn.clearInterval();
      }
      }, 1000);
       
    },
    seeking() {
      // this.onMusicPlaying();
      this.$refs.audiomusic.currentTime = (this.$refs.audiomusic.duration * this.seek) / 100;
      // console.log("seeking...");

    },
  },
  props: {
    msg: String,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@media only screen and (max-width: 768px) {
.scroll {
 display: none;
}  


.displaytv {
  margin: 5px;
  padding: 5px;
  /* position: fixed;
            width: 50%; */
}
.boxtv {
  border-radius: 5px;
  margin: 10px;
  /* background-color: #fff; */
  color: black;
  /* background-image: url('image/img2.jpg'); */
}

.col-md-6{
 width: 100%; 
}
  
}
.container {
  background-color: black;
  height: 100%;
  color: #fff;
  width: 100%;
}
.scroll {
  background-image: url("../assets/03-large.jpg");
  margin: 5px;
  font-weight: bolder;
  padding: 5px;
  border-radius: 10px;
  /* width: 400px; */
  height: 700px;
  overflow: auto;
  text-align: justify;
  box-sizing: border-box;
  background-color: white;
}
.col-md-4 {
  float: left;
  width: 30%;
}
.col-md-6 {
  float: left;
  width: 50%;
}
.col-md-5 {
  float: left;
  width: 45%;
}
.col-md-12 {
  /* float: left; */
  width: 100%;
}

.displaytv {
  margin: 5px;
  padding: 5px;
  /* position: fixed;
            width: 50%; */
}
.boxtv {
  border-radius: 5px;
  margin: 10px;
  /* background-color: #fff; */
  color: black;
  /* background-image: url('image/img2.jpg'); */
}

.addcol {
  width: 400px;
}
.btn {
  background-color: cornflowerblue;
  color: white;
  padding: 10px;
  border-radius: 3px;
  border: none;
  font-weight: bold;
}
.float-right {
  float: right;
  padding-bottom: 5px;
  position: relative;
}
.upload-btn {
  position: absolute;
  left: 0;
  opacity: 0;
}
.musics {
  margin-bottom: 25px;
  /* border-bottom: 2px solid cornflowerblue; */
  color: #fff;
}
.musics div:nth-child(1),
.musics div:nth-child(2) {
  padding-right: 50px;
}
.musics div {
  display: inline-block;
}

.music-name {
  text-align: center;
  padding-top: 20px;
  color: #fff;
  font-size: 20px;
  font-weight: bold;
}
.screen {
  background-color: black;
  border-radius: 3px;
  background-repeat: no-repeat;
  width: 80%;
  height: 350px;
  left: 10%;
  align-self: center;
  text-align: center;
  position: relative;
  color: azure;
}
.controls div {
  margin-right: 60px;
  display: inline-block;
}
.controls {
  text-align: center;
  font-weight: bolder;
  color: white;
  margin-left: 70px;
}
.active {
  background-color: #ccc;
  padding: 7px;
}
.volume {
  color: white;
  margin-left: 20px;
  padding-right: 40px;
  width: 100%;
}

.volrange,
.repeat {
  float: left;
  width: 85%;
}
.speaker,
.shuffle {
  float: left;
  width: 15%;
}
.functions {
  color: #fff;
}
</style>
