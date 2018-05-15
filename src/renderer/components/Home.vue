<template>
    <div class="container">
        <button @click="launchPlaylist">Lancer la playlist</button>
        <img @click="back()" class="fixed icon-arrow" src="./../assets/img/left-arrow.svg" alt="">
        <div class="row">
            <div v-for="(file, key) in files" class="col-xs-4 column">
                <div class="item" @click="open(file, key)">
                    <span @click.stop="deleteFile(file.path)" class="icon-cancel close"></span>
                    <div class="item-content">
                        <template v-if="file.type === 'directory'">
                            <img class="icon" src="./../assets/img/folder.svg" alt="">
                        </template>
                        <template v-else="file.type === 'file'">
                            <img class="icon" src="./../assets/img/file.svg" alt="">
                        </template>
                        <span>{{file.name}}</span>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
  import { exec } from 'child_process'
  import os from 'os'

  const serverUrl = 'http://dayze.fr'
  const port = '4041'
  export default {
    name: 'home',
    data () {
      return {
        history: [],
        tree: [],
        files: {}
      }
    },
    methods: {
      play (path) {
        let pathTovlc
        if (os.platform() === 'darwin') {
          pathTovlc = '/Applications/VLC.app/Contents/MacOS/VLC'
        } else if (os.platform() === 'win32') {
          pathTovlc = 'C:\\Program Files\\VideoLAN\\VLC\\vlc.exe'
        }
        console.log(path)
        exec(`${pathTovlc} ${path} --fullscreen`, (err) => {
          console.log(err)
        })
      },
      launchPlaylist () {
        let fullpath = ''
        for (let file of this.files) {
          fullpath += ` ${this.formatUrl(file.path)}`
        }
        console.log(fullpath)
        exec(`/Applications/VLC.app/Contents/MacOS/VLC ${fullpath} --fullscreen`, (err) => {
          console.log(err)
        })
      },
      open (file, key) {
        if (file.type === 'directory') {
          this.files = file.files
          this.history.push(key)
        } else {
          let url = this.formatUrl(file.path)
          this.play(url)
        }
      },
      deleteFile (file) {
        this.$http.post(`${serverUrl}:${port}/file`, {filePath: file}).then((resp) => {
          console.log(resp)
        }).catch((err) => {
          console.log(err)
        })
      },
      back () {
        let tempFiles
        if (this.history.length === 1 || this.history.length === 0) {
          this.files = this.tree
        } else {
          for (let i = 0; i < this.history.length - 1; i++) {
            let history = this.history[i]
            if (i === 0) {
              tempFiles = this.tree[history]
            } else {
              tempFiles = tempFiles.files[history]
            }
          }
          this.files = tempFiles.files
        }
        this.history.pop()
      },
      formatUrl (path) {
        let tempArr = path.split('/')
        tempArr.splice(0, 4)
        let url = serverUrl + '/' + tempArr.join('/').replace(/ /g, '%20').replace(/'/, '\\\'')
        return url
      }
    },
    mounted () {
      this.$http.get(`${serverUrl}:${port}/files`).then((resp) => {
        this.tree = resp.data
        this.files = this.tree
      }).catch((err) => {
        console.log(err)
      })
    }
  }
</script>

<style scoped lang="scss">
    .container {
        //margin: auto;
    }

    .fixed {
        position: fixed;
        top: 0;
        left: 20px;
    }

    .close {
        color: #c6c6c6;
        z-index: 2;
        position: absolute;
        right: 0;
        top: 5px;
        font-size: 30px;
        &:hover {
            color: rgb(90, 200, 250);
            cursor: pointer;
        }
    }

    .column {
        margin-bottom: 15px;
    }

    .item {
        box-shadow: 0 5px 3px -6px black;
        position: relative;
        font-family: "Helvetica";
        height: 100%;
        background-color: rgba(245, 245, 245, 0.8);
        border-radius: 10px;
        padding: 10px;
        margin-top: 15px;
        cursor: pointer;
        &:hover {
            background-color: rgba(240, 240, 240, 0.9);
        }
        .item-content {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
        }
        color: #141414;
    }

    .icon {
        max-width: 40px;
        margin-bottom: 10px;
    }

    .icon-arrow {
        max-width: 50px;
        margin-top: 15px;
        cursor: pointer;
    }

    /*.row {
        justify-content: space-evenly;
    }*/
</style>