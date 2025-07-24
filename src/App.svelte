<script>
  import { Play, Pause, SkipBack, SkipForward, FastForward, VolumeX, Volume1, Volume2, AlignJustify } from '@lucide/svelte';
  
  const playlist = [
		{
			artist: 'Twenty One Pilots',
			name: 'Routines in the Night',
			audio: '/audios/Routines in the Night.mp3',
      cover: '/Cover.jpg',
		},
    {
			artist: 'Twenty One Pilots',
			name: 'Navigating',
			audio: '/audios/Navigating.mp3',
      cover: '/Cover.jpg',
		},
    {
			artist: 'Twenty One Pilots',
			name: 'Midwest Indigo',
			audio: '/audios/Midwest Indigo.mp3',
      cover: '/Cover.jpg',
		},
	]

  let isPlaying = false;
  let trackIndex = 0;
  let audioFile = new Audio(playlist[trackIndex].audio);
  let volume = 15;
  let trackDuration;
  let currentTrackDuration;
  let totalTrackDuration;
  let progress = 0;


  audioFile.onloadedmetadata = () => {
    trackDuration = audioFile.duration;
    updateTime();
  }

  audioFile.ontimeupdate = () => {
    updateTime();

    if (audioFile.ended) {
      nextTrack();
    }
  }

  const updateTime = () => {
    // progress = (audioFile.currentTime / trackDuration) * 100;
    progress = audioFile.currentTime * (100  / trackDuration);

    let durMins = Math.floor((trackDuration % 3600) / 60);
    let durSecs = Math.floor(trackDuration % 60);

    let currMins = Math.floor((audioFile.currentTime % 3600) / 60);
    let currSecs = Math.floor(audioFile.currentTime % 60).toString().padStart(2, '0');

    totalTrackDuration = `${durMins}:${durSecs}`;
    currentTrackDuration = `${currMins}:${currSecs}`;
  }

  const playTrack = () => {
    if (isPlaying) {
      audioFile.pause();
      isPlaying = false;
      return;
    }
    audioFile.play();
    audioFile.volume = volume / 100; // Set initial volume
    isPlaying = true;
  }

  const nextTrack = () => {
    trackIndex = (trackIndex + 1) % playlist.length;
    audioFile.src = playlist[trackIndex].audio;
    if (isPlaying) {
      audioFile.play();
    }
  }

  const prevTrack = () => {
    trackIndex = (trackIndex - 1 + playlist.length) % playlist.length;
    audioFile.src = playlist[trackIndex].audio;
    if (isPlaying) {
      audioFile.play();
    }
  }

  const seekForward = () => {
    audioFile.currentTime += 10; // Seek forward by 10 seconds
    updateTime();
  }

  const seekBackward = () => {
    audioFile.currentTime -= 10; // Seek backward by 10 seconds
    updateTime();
  }

  addEventListener ('keydown', (event) => {
    if (event.key === ' ') {
      event.preventDefault(); // Prevent scrolling
      playTrack();
    } else if (event.key === 'ArrowRight') {
      nextTrack();
    } else if (event.key === 'ArrowLeft') {
      prevTrack();
    }
    else if (event.key === 'ArrowUp') {
      event.preventDefault();
      volume = Math.min(volume + 10, 100);
      audioFile.volume = volume / 100;
    } else if (event.key === 'ArrowDown') {
      event.preventDefault();
      volume = Math.max(volume - 10, 0);
      audioFile.volume = volume / 100;
    }
  });

</script>

<main>
  <img class="track-cover" src="{playlist[trackIndex].cover}" alt="track cover" />
  <h1>{playlist[trackIndex].name}</h1>
  <h2>{playlist[trackIndex].artist}</h2>

  <div class="time-info">
    <span class="progress-time">{currentTrackDuration}</span>

    <div class="progress-bar">
      <span class="bar" style="width: {progress}%;"></span>
    </div>

    <span class="track-duration">{totalTrackDuration}</span>
  </div>
  
  <section>
    <button onclick={seekBackward}>
      <!-- could not find a rewind icon lol -->
      <FastForward style="transform: rotate(180deg);" />
    </button>

    <button onclick={prevTrack}>
      <SkipBack/>
    </button>

    <button onclick= {playTrack}>
      {#if isPlaying}
        <Pause/>
      {:else}
        <Play/>
      {/if}
    </button>

    <button onclick={nextTrack}>
      <SkipForward/>
    </button>

    <button onclick={seekForward}>
      <FastForward/>
    </button>

  </section>

  <div class="slider-container">
    <label for="volume-slider">
      {#if audioFile.volume === 0}
        <VolumeX/>
      {:else if audioFile.volume < 0.5}
        <Volume1/>
      {:else}
        <Volume2/>
      {/if}
    </label>

    <input name="volume-slider" type="range" min="0" max="100" bind:value={volume}
          oninput={() => {
            audioFile.volume = volume / 100;
          }}
          class="range range-xs"
    />
    <label for="volume-slider">{volume}</label>
  </div>

  <div class="playlist">
    <div class="dropdown dropdown-center">
      <div tabindex="0" role="button" class="btn m-1">
            <AlignJustify size={24} />
          <h3>Playlist</h3>
      </div>
      <ul class="dropdown-content menu bg-base-100 rounded-box z-1 w-128 p-2 shadow-sm">
        {#each playlist as track, index}
          <li class="playlist-items">
            <button class={index === trackIndex ? 'active' : ''} onclick={() => {
            trackIndex = index;
            audioFile.src = track.audio;
            if (isPlaying) {
              audioFile.play();
            }
          }}>
              <Play size={16} />
            </button>
            {track.name} - {track.artist}
        </li>
        {/each}
      </ul>
    </div>
  </div>
</main>

<style>
  main {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    color: white;
  }

  h1, h2 {
    margin: 0;
  }

  section {
    display: flex;
    gap: 1rem;
    margin-top: 1rem;
  }

  button {
    padding: 0.5rem 1rem;
    background-color: transparent;
    border: none;
    cursor: pointer;
  }

  label {
    width: 24px;
    height: 24px;
  }

  .playlist {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 0.5rem;
    margin-top: 1rem;
    padding: 1rem;
  }

  .playlist-items {
    display: flex;
    align-items: center;
    /* gap: 0.5rem; */
    padding: 1rem;
    border-radius: 5px;
  }

  .playlist-items button {
    height: 30px;
    cursor: pointer;
  }

  .slider-container {
    display: flex;
    align-items: center;
    margin-top: 1rem;
    gap: 0.5rem;
  }

  input[type="range"] {
    width: 150px;
  }
  
  .track-cover {
    width: 300px;
    height: 300px;
    border-radius: 10px;
    object-fit: cover;
    margin: 1rem 0;
  }

  .time-info {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 1rem;
    width: 25%;
    margin-top: 1.5rem;
  }

  .time-info span {
    width: 50px;
  }

  .progress-bar {
    width: 100%;
    height: 5px;
    background-color: #555;
    border-radius: 2.5px;
    position: relative;
    overflow: hidden;
    cursor: pointer;
  }

  .progress-bar .bar {
    position: absolute;
    width: 30%;
    height: 100%;
    background-color: #f3f3f3;
    border-radius: 2.5px;
    transition: width 0.1s ease-in-out;
  }

  .progress-bar:hover .bar {
    background-color:salmon ;
  }

</style>