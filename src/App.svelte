<script>
  import { Play, Pause, SkipBack, SkipForward, VolumeX, Volume1, Volume2 } from '@lucide/svelte';
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
  let volume = 50;

  const playTrack = () => {
    if (isPlaying) {
      audioFile.pause();
      isPlaying = false;
      return;
    }
    audioFile.play();
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
  <section>
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
    />
    <label for="volume-slider">{volume}</label>
  </div>
  <div class="playlist">
    <h3>Playlist</h3>
    <div class="tracks">
      {#each playlist as track, index}
        <button class={index === trackIndex ? 'active' : ''} onclick={() => {
          trackIndex = index;
          audioFile.src = track.audio;
          if (isPlaying) {
            audioFile.play();
          }
        }}>
          {track.name} - {track.artist}
      </button>
      {/each}
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
    margin-top: 2rem;
  }

  .tracks {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .slider-container {
    display: flex;
    align-items: center;
    margin-top: 1rem;
    gap: 0.5rem;
  }

  input[type="range"] {
    width: 200px;
  }
  
  .track-cover {
    width: 300px;
    height: 300px;
    border-radius: 10px;
    object-fit: cover;
    margin-bottom: 1rem;
  }
</style>