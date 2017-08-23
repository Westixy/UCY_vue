<template>
  <v-app :dark="isDark" standalone>
    <v-layout row wrap :style="{height:'100vh',overflow:'auto'}">
      <v-flex sm4 class="flex-collumn">
        <div class="centerit triple">
          <v-btn v-on:click.left="playSound('entreeJoueur')" v-on:contextmenu="selectFile('entreeJoueur')" class="btn-simple yellow darken-2" primary>
            <v-icon v-if="paths.entreeJoueur===null">do_not_disturb_on</v-icon> Entrée joueurs</v-btn>
          <v-btn v-on:click.left="playSound('starting6')" v-on:contextmenu="selectFile('starting6')" class="btn-simple yellow darken-2" primary>
            <v-icon v-if="paths.starting6===null">do_not_disturb_on</v-icon> Starting 6</v-btn>
          <v-btn v-on:click.left="playSound('intro')" v-on:contextmenu="selectFile('intro')" class="btn-simple blue-grey darken-4" primary>
            <v-icon v-if="paths.intro===null">do_not_disturb_on</v-icon> Intro</v-btn>
        </div>
        <div class="centerit">
          <v-btn v-on:click.left="playSound('goalTeam1')" v-on:contextmenu="selectFile('goalTeam1')" class="btn-simple green darken-1" primary>
            <v-icon v-if="paths.goalTeam1===null">do_not_disturb_on</v-icon> Goal team 1</v-btn>
        </div>
        <div class="centerit">
          <v-btn v-on:click.left="playSound('pub')" v-on:contextmenu="selectFile('pub')" class="btn-simple deep-orange darken-2" primary>
            <v-icon v-if="paths.pub===null">do_not_disturb_on</v-icon> PUB</v-btn>
        </div>
      </v-flex>
      <v-flex sm4 class="flex-collumn">
        <div v-on:contextmenu="selectLogo" class="centerit">
          <span v-if="paths.logo===null">LOGO</span>
          <img v-else :src="paths.logo" class="mainlogo" />
        </div>
        <div class="centerit">
          <v-icon>arrow-left</v-icon>
          <v-btn v-on:click.left="playMult('arretsDeJeu')" v-on:contextmenu="selectFolder('arretsDeJeu')" class="btn-aj" primary>
            <div>
              <v-icon v-if="paths.arretsDeJeu===null">do_not_disturb_on</v-icon> Arrêts de jeu
              <br>
              <v-btn :disabled="mult.arretsDeJeu.sounds.length===0" fab :dark="isDark" small v-on:click.stop="nextSound(mult.arretsDeJeu)">
                <v-icon>keyboard_arrow_left</v-icon>
              </v-btn>
              <v-btn :disabled="mult.arretsDeJeu.sounds.length===0" fab :dark="isDark" small v-on:click.stop="nextSound(mult.arretsDeJeu)">
                <v-icon>keyboard_arrow_right</v-icon>
              </v-btn>
            </div>
          </v-btn>
          <v-text-field label="Now" readonly :value="path.basename(mult.arretsDeJeu.actual===null?'':mult.arretsDeJeu.actual)" class="actual" />
          <br>
          <v-text-field label="Next" readonly :value="path.basename(mult.arretsDeJeu.next===null?'':mult.arretsDeJeu.next)" class="next" />
        </div>
        <div class="centerit">
          <v-btn v-on:click.left="playTimeout()" v-on:contextmenu="selectFile('timeOut')" class="btn-simple purple darken-2" primary>
            <v-icon v-if="paths.timeOut===null">do_not_disturb_on</v-icon> Time out
            <v-btn fab :dark="isDark">
              {{timeout.time-1}}
            </v-btn>
          </v-btn>
        </div>
      </v-flex>
      <v-flex sm4 class="flex-collumn">
        <div class="centerit">
          <v-btn v-on:click.left="playSound('penality')" v-on:contextmenu="selectFile('penality')" class="btn-simple red darken-2" primary>
            <v-icon v-if="paths.penality===null" >do_not_disturb_on</v-icon> Penality</v-btn>
          <v-btn class="btn-simple pink darken-1" primary  v-on:click.left="playSoundSpe()" v-on:contextmenu="selectFile('minutes2')">
            <v-icon v-if="paths.minutes2===null" >do_not_disturb_on</v-icon>2 Minutes 
             <v-btn fab :dark="isDark" small v-on:click.stop="resetSoundSpe()">
                <v-icon>update</v-icon>
              </v-btn>
          </v-btn>
        </div>
        <div class="centerit">
          <v-btn v-on:click.left="playSound('goalTeam2')" v-on:contextmenu="selectFile('goalTeam2')" class="btn-simple green darken-1" primary>
            <v-icon v-if="paths.goalTeam2===null">do_not_disturb_on</v-icon> Goal team 2</v-btn>
        </div>
        <div class="centerit">
          <v-btn v-on:click.left="playMult('ambiance')" v-on:contextmenu="selectFolder('ambiance')" class="btn-simple lime darken-2" primary>
            <v-icon v-if="paths.ambiance===null">do_not_disturb_on</v-icon> Ambiance</v-btn>
        </div>
      </v-flex>
    </v-layout>
  
    <v-snackbar :timeout="toast.timeout" bottom left v-model="toast.model">
      {{ toast.text }}
      <v-btn flat class="pink--text" @click.native="snackbar = false">Close</v-btn>
    </v-snackbar>
  
    <audio ref="audio"></audio>

    <audio ref="audioSpe"></audio>
  
    <v-layout row justify-center>
      <v-dialog v-model="dialog" fullscreen transition="dialog-bottom-transition" :overlay="false">
        <v-btn class="float-bot-btn" slot="activator" fab small>
          <v-icon>settings</v-icon>
        </v-btn>
        <v-card>
          <v-toolbar>
            <v-btn icon @click.native="dialog = false">
              <v-icon>close</v-icon>
            </v-btn>
            <v-toolbar-title>Settings</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-toolbar-items>
              <v-btn flat @click.native="dialog = false">Fermer</v-btn>
            </v-toolbar-items>
          </v-toolbar>
          <v-list three-line subheader>
            <v-list-tile avatar>
              <v-list-tile-action>
                <v-checkbox v-model="isDark"></v-checkbox>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>Thême foncé</v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list>
          <v-list three-line subheader>
            <v-list-tile avatar>
              <v-list-tile-action>
                <v-checkbox v-model="mult.restartAfterStop"></v-checkbox>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>Relancer la Musique au début apres une pause
                  <em>(arrêts de jeu, ambiance)</em>
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list>
          <v-list three-line subheader>
            <v-list-tile avatar v-on:click="loadConfig()">
              <v-list-tile-action>
                <v-icon>file_download</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>Charger la configuration
                  <em style="font-size:smaller;">{{configFile}}</em>
                </v-list-tile-title>
                <v-list-tile-sub-title>
                  <v-btn outline v-on:click.stop="chooseConfigFile" small>Choisir le fichier de config</v-btn>
                </v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list>
          <v-list three-line subheader>
            <v-list-tile avatar v-on:click="saveConfig()">
              <v-list-tile-action>
                <v-icon>save</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>Sauver la configuration
                  <em style="font-size:smaller;">{{configFile}}</em>
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list>
          <!--<v-divider></v-divider>-->
        </v-card>
      </v-dialog>
    </v-layout>
  
  </v-app>
</template>

<script>
import { remote } from 'electron'
import fs from 'fs'
import path from 'path'

const encodeSrc= (path)=>{
  return `file://${encodeURI(path).replace(/\#/g, '%23').replace(/\?/g, '%3F')}`
}

export default {
  data() {
    return {
      path,
      isDark: true,
      configFile: './.UCY_app_config.json',
      paths: {
        entreeJoueur: null,
        starting6: null,
        intro: null,
        penality: null,
        minutes2: null,
        logo: null,
        goalTeam1: null,
        goalTeam2: null,
        arretsDeJeu: null,
        pub: null,
        timeOut: null,
        ambiance: null
      },
      mult: {
        arretsDeJeu: {
          selected: 0,
          sounds: [],
          actual: null,
          next: null
        },
        ambiance: {
          selected: 0,
          sounds: [],
          actual: null,
          next: null
        },
        restartAfterStop: false
      },
      timeout:{
        time:31,
        handler:null
      },
      volume:100,
      musicPath: null,
      dialog: false,
      toast: {
        timeout: 1500,
        text: '',
        model: false
      },
      soundSpe:{
        playing:false,
      },
      music: {
        playing: false,
        switch: (path) => {
          this.$refs.audio.src = encodeSrc(path)
        },
        play: () => {
          this.music.playing = true
          this.$refs.audio.play().catch(err => {
            this.music.playing = false
            console.error(this.$refs.audio.src)
            console.error(err)
          })
        },
        stop: () => {
          this.$refs.audio.pause()
          this.music.playing = false
          if(this.timeout.handler!==null){
            clearInterval(this.timeout.handler)
            this.timeout.handler=null
            this.timeout.time=31
          }
        },
        setOnEnd: (cb) => {
          console.trace(this)
          this.$refs.audio.onended = cb
        }
      }
    }
  },
  components: {
  },
  methods: {
    showDialog(options, cb) {
      setTimeout(() => remote.dialog.showOpenDialog(remote.getCurrentWindow(), options, cb), 200)
    },
    selectLogo() {
      this.showDialog({
        title: 'Choisir un fichier pour le logo',
        defaultPath: this.paths.logo || __dirname + '/..',
        filters: [{ name: 'Images', extentions: ['jpg', 'png', 'gif', 'svg'] }],
        properties: ['openFile', 'showHiddenFiles']
      }, (files) => {
        if (files === undefined) return undefined
        const file = files[0]
        this.paths.logo = file
      })
    },
    selectFile(name) {
      if (this.paths[name] === undefined) return undefined
      this.showDialog({
        title: 'Choisir un fichier pour : ' + name,
        defaultPath: this.paths[name] || __dirname,
        filters: [{ name: 'Musique', extentions: ['mp3', 'wav', 'ogg'] }],
        properties: ['openFile', 'showHiddenFiles']
      }, (files) => {
        if (files === undefined) return undefined
        const file = files[0]
        this.paths[name] = file
      })
    },
    selectFolder(name) {
      if (this.paths[name] === undefined) return undefined
      this.showDialog({
        title: 'Choisir un dossier pour : ' + name,
        defaultPath: this.paths[name] || __dirname,
        properties: ['openDirectory', 'showHiddenFiles']
      }, (files) => {
        if (files === undefined) return undefined
        const file = files[0]
        this.paths[name] = file
      })
    },
    saveConfig() {
      fs.writeFile(this.configFile, JSON.stringify({
        configFile: this.configFile,
        isDark: this.isDark,
        paths: this.paths,
        restartAfterStop:this.mult.restartAfterStop
      }),
        (err) => {
          if (err) {
            this.showToast('Error while writing configfile')
          } else {
            this.showToast('Config saved :)')
          }
        })

    },
    loadConfig(path = this.configFile) {
      fs.stat(path, (err, stats) => {
        if (err) {
          this.showToast('Config file does not exist : ' + path)
          console.error(err)
        }
        else {
          fs.readFile(this.configFile, (err, data) => {
            if (err) {
              this.showToast('Config cannot be loaded ...')
              console.error(err)
            } else {
              try {
                const obj = JSON.parse(data)
                if (obj.paths !== undefined) this.paths = obj.paths
                if (obj.isDark !== undefined) this.isDark = obj.isDark
                if (obj.configFile !== undefined) this.configFile = obj.configFile
                if (obj.restartAfterStop !== undefined) this.mult.restartAfterStop = obj.restartAfterStop
                this.showToast('Config loaded :)')
              } catch (ex) {
                console.error(ex)
                this.showToast('Config file is corrupt (JSON syntax)')
              }
            }
          })
        }
      })
    },
    chooseConfigFile() {
      this.showDialog({
        title: 'Choisir le fichier de config',
        defaultPath: this.configFile || __dirname,
        filters: [{ name: 'Tout', extentions: ['*'] }],
        properties: ['openFile', 'showHiddenFiles']
      }, (files) => {
        if (files === undefined) return undefined
        const file = files[0]
        this.configFile = file
      })
    },
    showToast(text, timeout = 2500) {
      this.toast.text = text
      this.toast.timeout = timeout
      this.toast.model = true
    },
    playSound(what, { restart = true, onEnd = () => { }, defpath } = {}) {
      if(this.pauseSoundSpe()) return ;
      const path = defpath || this.paths[what]
      if(this.music.playing===true) return this.music.stop()
      const same = (this.$refs.audio.src === encodeSrc(path))
      if (path === undefined || path === null) {
        this.showToast('Music not set for : ' + what)
      } else {
        this.music.setOnEnd(() => onEnd())
        if (same === false) {
          this.music.stop()
        }
        if (this.music.playing === false) {
          if (same === false || restart === true) this.music.switch(path)
          this.music.play()
        } else {
          this.music.stop()
        }
      }
    },
    setVolume(num){
      this.$refs.audio.volume=num/100;
      this.$refs.audioSpe.volume=num/100;      
    },
    playTimeout(){
      this.playSound('timeOut')
      if(this.music.playing===true){
        this.timeout.handler= setInterval(()=>{
        this.timeout.time-= 1
        if(this.timeout.time===0){
          this.music.stop()
          this.timeout.handler=null
          this.timeout.time=31
        } 
        console.log(this.timeout.time)
      },1000)
      }
      
    },
    playSoundSpe(){
      if(this.$refs.audioSpe.src!==encodeSrc(this.paths.minutes2)){
        this.$refs.audioSpe.src=encodeSrc(this.paths.minutes2)
      }
      if(this.music.playing===true) {
        return this.music.stop()
      }
      if(!this.pauseSoundSpe()){
        this.$refs.audioSpe.play()
        this.soundSpe.playing=true
      }
    },
    pauseSoundSpe(){
      if(this.soundSpe.playing===true){
        this.$refs.audioSpe.pause()
        this.soundSpe.playing=false
        return true
      }else return false
    },
    resetSoundSpe(){
      this.pauseSoundSpe()
      this.$refs.audioSpe.src=encodeSrc(this.paths.minutes2)
    },
    playMult(what, force = false) {
      const mult = this.mult[what]
      const playing = this.music.playing
      if (mult === undefined) {
        this.showToast('Multiple does not exist : ' + what)
      } else {
        if (force === true) {
          mult = {
            selected: 0,
            sounds: [],
            actual: null,
            next: null
          }
        }
        if (mult.sounds.length === 0) {
          const files = fs.readdirSync(this.paths[what])
          console.log(files)
          for (const file of files) {
            const ext = file.split('.').pop()
            if (['mp3', 'ogg', 'wav'].indexOf(ext.toLowerCase()) === -1) continue;
            mult.sounds.push(path.join(this.paths[what], file))
          }
          if (mult.sounds.length === 0) this.showToast('Folder doesn\'t have any music')
        }
        this.multSetTracks(mult)
        if (mult.actual === null) return this.music.stop()
        if (this.$refs.audio.src === encodeSrc(mult.actual) && playing === true) this.nextSound(mult) 
        this.playSound(undefined, { onEnd: () => this.nextSound(mult), defpath: mult.actual, restart: this.mult.restartAfterStop })
      }
    },
    prevSound(mult) {
      mult.selected = (mult.selected > 0) ? mult.selected - 1 : mult.sounds.length - 1
      this.multSetTracks(mult)
      if (mult.actual === null) return this.music.stop()
      this.music.switch(mult.actual)
      this.music.play()
    },
    nextSound(mult) {
      const playing = this.music.playing
      mult.selected = (mult.selected < mult.sounds.length - 1) ? mult.selected + 1 : 0
      this.multSetTracks(mult)
      if (mult.actual === null) return this.music.stop()
      this.music.switch(mult.actual)
      if(playing===true) this.music.play()
    },
    multSetTracks(mult) {
      if (mult.sounds.length === 0) {
        mult.actual = null
        mult.next = null
        return;
      }
      mult.actual = mult.sounds[mult.selected]
      mult.next = (mult.sounds.length > mult.selected + 1) ? mult.sounds[mult.selected + 1] : mult.sounds[0]
    }
  },
  mounted() {
    this.loadConfig()
  }
}
</script>

