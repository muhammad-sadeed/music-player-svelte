<script>
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
      prev
    </button>
    <button onclick= {playTrack}>
      {isPlaying ? 'pause' : 'play'}
    </button>
    <button onclick={nextTrack}>
      next
    </button>
  </section>
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
    background-color: #007bff;
    color: white;
    border: none;
    border-radius: 0.25rem;
    cursor: pointer;
    font-size: 1.25rem;
  }
</style>