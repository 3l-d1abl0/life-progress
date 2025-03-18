<script lang="ts">
    import ProgressBar from '../lib/ProgressBar.svelte';
    import Modal from '../lib/Modal.svelte';

  let showModal = false;
  let yearProgress = 60;
  let monthProgress = 70;
  let weekProgress = 40;
  let dayProgress = 90;
  let lifeProgress = 30;
  let title = 'Time Progress';
</script>

<main>
    <div class="progress-container">
      <h1 class="progress-title">{title}</h1>
      <ProgressBar label="Year" percentage={yearProgress} />
      <ProgressBar label="Month" percentage={monthProgress} />
      <ProgressBar label="Week" percentage={weekProgress} />
      <ProgressBar label="Day" percentage={dayProgress} />
      <ProgressBar label="Life" percentage={lifeProgress} />
    </div>
  
    <Modal />
</main>


  <style>
    main {
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #f0f0f0;
      padding: 1rem;
    }
  
    .progress-container {
      background: #f0f0f0;
      padding: 2rem;
      border-radius: 20px;
      box-shadow: 20px 20px 60px #bebebe,
                  -20px -20px 60px #ffffff;
      width: 90%;
      max-width: 600px;
    }
  
    h1 {
      text-align: center;
      color: #333;
      margin-bottom: 2rem;
      font-size: 2rem;
    }

    h1.progress-title{
        margin-bottom: 0rem;
    }
  
    @media (max-width: 600px) {
      .progress-container {
        padding: 1rem;
      }
  
      h1 {
        font-size: 1.5rem;
      }
    }
  </style>