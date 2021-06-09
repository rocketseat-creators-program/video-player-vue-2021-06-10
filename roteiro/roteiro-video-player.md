# Roteiro video player customizado com Vue

## Introdução

- [ ] Overview sobre o projeto
- [ ] Requisitos
- [ ] DOM e Eventos

Links de referência:
- https://www.w3schools.com/tags/tag_video.asp
- https://www.w3schools.com/tags/ref_av_dom.asp

## Criando componente VideoPlayer

- [ ] Criação do projeto

Para criar o projeto utilizaremos o vue cli
```
vue create video-player
```
Adiciona o Vuetify
```
vue add vuetify
```
Adiciona os ícones
```
npm install @mdi/font -D
```

Adiciona o seguinte código no arquivo **App.vue**
```html
<template>
  <v-container>
    <v-row justify="center">
      <v-col md="6">
        <!-- video vai aqui -->
      </v-col>
    </v-row>
  </v-container>
</template>
```

- [ ] Criar componente VideoPlayer

Link para utilizar no vídeo: https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4
```html
<video controls>
  <source src="https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4" type="video/mp4"/>
</video>
```

- [ ] Torna a tag de vídeo uma referência no DOM para poder manipular eventos a partir de outros elementos
```html
<video ref="videoPlayer">
```
- [ ]  Criar container onde dividiremos as seções entre vídeo e controladores

```html
<div class="video__container">
  <video ref="videoPlayer">
    <source src="https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4"/>
  </video>
  <div class="video__controls"></div>
</div>
```

```css
.video__container {
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  cursor: pointer;
  background: #000;
  min-width: 600px;
  min-height: 340px;
  margin: 0 auto;
}
```

## Seção de controle

- [ ] Remove atributo control da tag `<video>`
```html
```
- [ ] Adiciona CSS do container da seção de controle
```css
:root {
  --primary-color: #f17;
  --control-background-color: rgba(45, 45, 45, 0.8);
}

.video__controls {
  position: absolute;
  bottom: 10px;
  background: var(--control-background-color);
  padding: 15px;
  z-index: 2;
  width: 100%;
  align-items: center;
  color: #fff;
  border-radius: 6px;
}
```

## Play/Pause

- [ ] Cria botão que dá play no vídeo
- [ ] Cria toggle para dar pause com click.stop para parar a propagação do evento
```html
<div class="video__controls">
  <div>
    <!-- espaço para fazer a barra de progresso !-->
  </div>
  <div>
    <div>
      <button @click.stop="togglePlay">
        <v-icon v-if="isPlaying" style="color:#fff;">mdi-pause</v-icon>
        <v-icon v-else style="color:#fff;">mdi-play<v-icon>
      </button>
    </div>
  </div>
</div>
```

```js
data(){
  return {
    isPlaying:false
  }
}
```

```js
togglePlay(){
  if (this.$refs?.videoPlayer.paused) {
    this.isPlaying = true;
    this.$refs.videoPlayer.play();
  } else {
    this.isPlaying = false;
    this.$refs.videoPlayer.pause();
  }
}
```

**PSC:** _Poderíamos pensar em mover essa lógica de está tocando ou não para algum método dentro de **computed**. Mas $refs não são reativas e são populadas após a renderização do componente. Devemos evitar utilizar $refs dentro de computed e templates. [Veja mais sobre isso aqui](https://vuejs.org/v2/guide/components-edge-cases.html#Accessing-Child-Component-Instances-amp-Child-Elements)_

## Duração do vídeo e progresso em segundos

- [ ] Download de frames e meta data que o browser faz

Links para referência:
- https://www.w3schools.com/tags/av_prop_duration.asp
- https://www.w3schools.com/tags/av_event_loadedmetadata.asp
- https://developer.mozilla.org/en-US/docs/Web/Guide/Audio_and_video_delivery/buffering_seeking_time_ranges

- [ ] Adiciona html para o tempo de duração

```html
<div class="video__controls__duration">
  <span>{{ durationTime }}</span>
</div>
```
- [ ] Cria método que atualiza uma data property para duração
```js
data(){
  return {
    isPlaying:false,
    duration: 0
  }
}

//adicionar em methods
updateVideoDetails() {
  if (this.$refs.videoPlayer) {
    if (!Number.isNaN(this.$refs.videoPlayer.duration)) {
      this.duration = this.$refs.videoPlayer.duration;
    }
  }
}
```
- [ ] Cria função que formata tempo
```js
formatTime(num) {
      let hours = Math.floor(num / 3600);
      let minutes = Math.floor((num % 3600) / 60);
      let seconds = Math.floor(num % 60);

      hours = hours < 10 ? "0" + hours : hours;
      minutes = minutes < 10 ? "0" + minutes : minutes;
      seconds = seconds < 10 ? "0" + seconds : seconds;

      if (hours > 0) {
        return hours + ":" + minutes + ":" + seconds;
      }
      return minutes + ":" + seconds;
    }
```

```js
computed: {
  durationFormatted() {
    return this.formatTime(this.duration);
  }
}
```

- [ ] Chama método **updateVideoDetails()** no **@loadedmetadata**

```html
<video ref="videoPlayer" @loadedmetadata="updateVideoDetails">
```

- [ ] Cola estilo para deixar elementos um do lado do outro

```html
 <div class="video__controls__functions">
```

```css
.video__controls__duration {
  justify-self: flex-end;
  margin: 3px;
}

.video__controls__duration span {
  margin: 0 3px;
}

.video__controls__functions {
  display: grid;
  grid-template-columns: 1fr 1fr;
  width: 100%;
}

.video__controls__functions > div {
  display: flex;
}
```

- [ ] Cria data property para current time
```js
data(){
  return {
    isPlaying:false,
    duration:0,
    currentTime:0
  }
}
```

- [ ] Atualiza this.currentTime no método updateVideoDetials() a partir da referência

```js
updateVideoDetails(){
  if(this.$refs.videoPlayer){
      if (!Number.isNaN(this.$refs.videoPlayer.duration)) {
        this.duration = this.$refs.videoPlayer.duration;
      }
      this.currentTime = this.$refs.videoPlayer.currentTime;
  }
}
```

- [ ] Adiciona tempo formatado
```js
currentTimeFormatted() {
  return this.formatTime(this.currentTime);
}
```

```html
<span>{{ currentTimeFormatted }}</span>/<span>{{ durationFormatted }}</span>
```

- [ ] Adiciona evento @timeupdate para sempre atualizar os valores

@timeupdate é invocado pelo play/pause e também acontece sempre que o currentTime muda
```html
<video
  ref="videoPlayer"
  @loadedmetadata="updateVideoDetails"
  @timeupdate="updateVideoDetails"
>
```

## Barra de progresso

- [ ] Criação de elemento como referência no DOM para representar o progresso do vídeo

```html
<div class="video__controls__progress__container">
  <div
    ref="videoPlayerProgress"
    class="video__controls__progress"
  >
    <div class="video__controls__progress__track">
      <div class="video__controls__progress__track--watching"></div>
    </div>
  </div>
</div>
```

```css
.video__controls__progress__container {
  display: flex;
  width: 100%;
}

.video__controls__progress {
  width: 100%;
  flex: 1;
  display: flex;
  align-items: center;
  margin: 0 0.45rem;
  background: #000;
  height: 0.25rem;
  position: relative;
}

.video__controls__progress__track {
  background: var(--primary-color);
  transition: width 0.2ms;
  display: flex;
  height: 0.25rem;
  border-radius: 6px;
}

.video__controls__progress__track--watching {
  height: 1.3rem;
  width: 0.5rem;
  border-radius: 20px;
  background: #fff;
  border: 1px solid #000;
  position: absolute;
  left: 0;
  bottom: -0.5rem;
}
```

- [ ] Criação do método mudar o progresso de acordo com a região que clicar

Para que o evento não seja propagado adicionamos .prevent.stop
```html
<div
  ref="videoPlayerProgress"
  class="video__controls__progress"
  @click.prevent.stop="handleTrackClick"
>
```

```js
handleProgressClick(event) {
  const currentTime =
    (this.duration * event.offsetX) /
    this.$refs.videoPlayerProgress.offsetWidth;
  this.currentTime = currentTime;
  this.$refs.videoPlayer.currentTime = currentTime;
},
```

Links de referência:
https://www.w3schools.com/jsref/tryit.asp?filename=tryjsref_event_mouse_offsetx
https://developer.mozilla.org/pt-BR/docs/Web/API/HTMLElement/offsetWidth

- [ ] Criação de computed property que representa o progresso
```js
progress() {
  return (this.currentTime / this.duration) * 100;
}
```

- [ ] Adiciona propriedades CSS que serão modificadas com a mudança de tempo
```html
<div class="video__controls__progress__container">
  <div
    ref="videoPlayerProgress"
    class="video__controls__progress"
  >
    <div
      class="video__controls__progress__track"
      :style="{ width: progress + '%' }"
    >
      <div
        class="video__controls__progress__track--watching"
        :style="{ left: progress + '%' }"
      >
      </div>
    </div>
  </div>
</div>
```

## Spinner
- [ ] Adiciona template do **spinner**

Vamos utilizar o componente v-progress-circular que o Vuetify, saiba mais aqui no link:
https://vuetifyjs.com/en/components/progress-circular/
```html
<div class="spinner" v-if="spinner">
  <v-progress-circular
    indeterminate
    color="var(--primary-color)"
  ></v-progress-circular>
</div>
```

```css
.spinner {
  z-index: 2;
  position: absolute;
}
```

```js
data(){
  return {
    isPlaying:false,
    duration:0,
    currentTime:0
    spinner:true
  }
}
```

- [ ] **@waiting** é disparado quando o vídeo precisa baixar o próximo frame
```html
 <video
  ref="videoPlayer"
  @loadedmetadata="updateVideoDetails"
  @timeupdate="updateVideoDetails"
  @waiting="spinner = true"
>
  <source src="https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4" type="video/mp4"/>
</video>
```
- [ ] **@canplay** é disparado quando o vídeo pode ser tocado sem precisar baixar o próximo frame
```html
 <video
  ref="videoPlayer"
  @loadedmetadata="updateVideoDetails"
  @timeupdate="updateVideoDetails"
  @waiting="spinner = false"
>
  <source src="https://archive.org/download/BigBuckBunny_124/Content/big_buck_bunny_720p_surround.mp4" type="video/mp4"/>
</video>
```

## Volume
Volume também é disponiblizado no nosso objeto de referência pelo DOM na propriedade .volume e vai de 0.0 à 1.0.
veja mais aqui: https://www.w3schools.com/tags/av_prop_volume.asp

- [x] Adiciona ícone e template na barra de controladores (o play/pause e tempo precisam ser englobados em uma div, e o volume entrou para alinharmos um à esquerda e outro à direita)

```html
<div class="video__controls__configs">
  <div class="video__controls__volume">
     <button>
        <v-icon v-if="" style="color:#fff">mdi-volume-low</v-icon>
        <v-icon v-else-if="volume >= 0.1 && volume < 0.8" style="color: #fff">mdi-volume-medium</v-icon>
        <v-icon v-else-if="volume >= 0.8" style="color: #fff" >mdi-volume-high</v-icon >
      </button>
  </div>
</div>
```

```js
data(){
  return {
    isPlaying:false,
    duration:0,
    currentTime:0
    spinner:true,
    volume: 0.3
  }
}
```

```css
.video__controls__functions .video__controls__configs {
  display: flex;
  justify-self: flex-end;
}

.video__controls__volume {
  position: relative;
}
```

- [x] Adiciona track para que o usuário possa clicar e alterar o valor do volume

```html
<div
  v-if="volumeOptionsOpen"
  class="video__controls__volume--options"
  ref="videoVolumeTrack"
>
  <div class="video__controls__volume--track">
    <div class="video__controls__volume--track-current" />
    <div class="video__controls__volume--track-ball" />
  </div>
</div>
```

```css
.video__controls__volume--options {
  position: absolute;
  height: 100px;
  padding: 5px 8px;
  left: 5px;
  top: -145px;
  background: var(--control-background-color);
  border-radius: 0.25rem;
}

.video__controls__volume--track {
  position: relative;
  height: 100%;
  width: 4px;
  border-radius: 6px;
  background-color: #8c8c8c;
}

.video__controls__volume--track-current {
  background-color: var(--primary-color);
  position: absolute;
  bottom: 0;
  left: 0;
  width: 4px;
}

.video__controls__volume--track-ball {
  width: 20px;
  height: 0.9375rem;
  border-radius: 13px;
  position: absolute;
  background-color: #fff;
  border: 0.125rem solid #222;
  left: -7px;
}
```

```js
data(){
  return {
    isPlaying:false,
    duration:0,
    currentTime:0
    spinner:true,
    volume: 0.3,
    volumeOptionsOpen: true
  }
}
```

- [ ] Cria método para aumentar volume
```html
<div
  v-if="volumeOptionsOpen"
  class="video__controls__volume--options"
  ref="videoVolumeTrack"
  @click.stop="handleVolumeClick"
>
```

```js
handleVolumeClick(event) {
  const volume = this.$refs.videoVolumeTrack;
  const currentVolume =
    (volume.getBoundingClientRect().top -
      event.pageY + volume.offsetHeight) / 100;
  if (currentVolume > 1) {
    this.volume = currentVolume;
    this.$refs.videoPlayer.volume = 1;
  } else if (currentVolume < 0.1) {
    this.volume = 0;
    this.$refs.videoPlayer.volume = 0;
  } else {
    this.volume = currentVolume;
    this.$refs.videoPlayer.volume = currentVolume;
  }
}
```

## Velocidade do vídeo
Assim como volume e currentTime, a velocidade também é representada pela propriedade playbackRate e 1.0 quer dizer que o vídeo está na sua velocidade normal

- [ ] Adicionar template de ícone
```html
<div class="video__controls__speed">
  <button>
    <span>
      <v-icon style="color: #fff">mdi-speedometer</v-icon>
    </span>
  </button>
</div>
```

```css
.video__controls__speed {
  position: relative;
  min-width: 60px;
  padding: 0px 15px;
  top: 0;
}

.video__controls__speed__options {
  position: absolute;
  left: 0;
  top: -220px;
  background: var(--control-background-color);
  border-radius: 0.25rem;
}
.video__controls__speed__options .active {
  background: rgba(255, 255, 255, 0.2);
}

.video__controls__speed__options div {
  padding: 5px;
}

.video__controls__speed__options div:hover {
  background: rgba(255, 255, 255, 0.35);
  padding: 5px;
  color: #000;
}
```

- [ ] Adiciona opções para mudar velocidade em que o vídeo está sendo reproduzido
```html
<div v-if="speedOpen" class="video__controls__speed__options">
  <div
    @click.stop="handleVideoPlaybackRate(2.0)"
    :class="{ active: !!(speed === '2x') }"
  >
    2x
  </div>
  <div
    @click.stop="handleVideoPlaybackRate(1.75)"
    :class="{ active: !!(speed === '1.75x') }"
  >
    1.75x
  </div>
  <div
    @click.stop="handleVideoPlaybackRate(1.5)"
    :class="{ active: !!(speed === '1.5x') }"
  >
    1.5x
  </div>
  <div
    @click.stop="handleVideoPlaybackRate(1.0)"
    :class="{ active: !!(speed === '1x') }"
  >
    1x
  </div>
  <div
    @click.stop="handleVideoPlaybackRate(0.75)"
    :class="{ active: !!(speed === '0.75x') }"
  >
    0.75x
  </div>
  <div
    @click.stop="handleVideoPlaybackRate(0.5)"
    :class="{ active: !!(speed === '0.5x') }"
  >
    0.5x
  </div>
</div>
```

- [ ] Adiciona click para abrir as opções de mudar velocidade

```html
<div class="video__controls__speed">
  <button @click.stop="speedOpen = !speedOpen">
    <span>
      <v-icon style="color: #fff">mdi-speedometer</v-icon>
    </span>
  </button>
</div>
```

- [ ] Cria método para mudar a velocidade
```js
data(){
  return {
    isPlaying:false,
    duration:0,
    currentTime:0
    spinner:true,
    volume: 0.3,
    volumeOptionsOpen: true,
    speedOpen: true,
    speed: "1x",
  }
}

handleVideoPlaybackRate(speed) {
  this.speed = `${speed}x`;
  this.$refs.videoPlayer.playbackRate = speed;
  if (this.speedOpen) {
    this.speedOpen = false;
  }
}
```

## Ajustes finais de animação na barra de controle do vídeo

- [ ] Adiciona data property para exibir a barra de progresso
```html
  <div class="video__controls" v-if="showProgressBar">
```

```js
data(){
  return {
    isPlaying:false,
    duration:0,
    currentTime:0
    spinner:true,
    volume: 0.3,
    volumeOptionsOpen: true,
    speedOpen: true,
    speed: "1x",
    showProgressBar:false
  }
}
```

- [ ] Adiciona data property para exibir os outros controladores.
```html
  <div v-if="showFunctions" class="video__controls__functions">
```

```js
data(){
  return {
    isPlaying:false,
    duration:0,
    currentTime:0
    spinner:true,
    volume: 0.3,
    volumeOptionsOpen: true,
    speedOpen: true,
    speed: "1x",
    showProgressBar:false,
    showFunctions:false
  }
}
```

- [ ] Adiciona eventos de mouse no wrapper do vídeo para quando o mouse estiver por cima venha exibir as barras

```html
<div
  class="video__container"
  @click="toggleVideoPlay"
  @mouseover="handleShowFunctions"
  @mouseleave="setTimeoutFunction"
>
```

```js
handleShowFunctions() {
  this.showFunctions = true;
  this.showProgressBar = true;
}

setTimeoutFunction() {
  const self = this;
  setTimeout(() => {
    self.showFunctions = false;
    self.speedOpen = false;
    self.volumeOptionsOpen = false;
  }, 6000);

  setTimeout(() => {
    self.showProgressBar = false;
  }, 10000);
}
```

```html
<div
  class="video__controls__progress__container"
  :style="`margin-bottom:${showFunctions ? '20px' : '0'}`"
>
```

### Conclusão

Este projeto nos faz ter uma noção melhor de recursos do DOM que podemos utilizar a respeito de mídias.