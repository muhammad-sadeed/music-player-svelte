<script>
  import { Play, Pause, SkipBack, SkipForward } from '@lucide/svelte';
  const playlist = [
		{
			artist: 'Twenty One Pilots',
			name: 'Routines in the Night',
			audio: '/audios/Routines in the Night.mp3',
		},
    {
			artist: 'Twenty One Pilots',
			name: 'Navigating',
			audio: '/audios/Navigating.mp3',
		},
    {
			artist: 'Twenty One Pilots',
			name: 'Midwest Indigo',
			audio: '/audios/Midwest Indigo.mp3',
		},
	]

  let isPlaying = false;
  let trackIndex = 0;
  let audioFile = new Audio(playlist[trackIndex].audio);

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

</script>

<main>
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
</style>