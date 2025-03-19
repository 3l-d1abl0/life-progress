<script lang="ts">
  import { onMount } from 'svelte';
  import ProgressBar from '../lib/ProgressBar.svelte';
  import Modal from '../lib/Modal.svelte';
  import {
      startOfYear,
      startOfMonth,
      startOfWeek,
      startOfDay,
      endOfYear,
      endOfMonth,
      endOfWeek,
      endOfDay,
      differenceInMilliseconds,
      parse
    } from 'date-fns';

  let showModal = $state(false);
  
  let yearProgress = $state(0);
  let monthProgress = $state(100);
  let weekProgress = $state(0);
  let dayProgress = $state(100);
  let lifeProgress = $state(0);
  let title = $state('Time Progress');

  let modal: Modal;

  //Calculate Percentage
  function calculateProgress(start: Date, end: Date, current: Date = new Date()): number {
    const total = differenceInMilliseconds(end, start);
    const elapsed = differenceInMilliseconds(current, start);
    return (elapsed / total) * 100;
  }

  function updateProgress() {
    //console.log('Updating Progress !');
    const now = new Date();
    const year = now.getFullYear();

    yearProgress = calculateProgress(startOfYear(now), endOfYear(now));
    monthProgress = calculateProgress(startOfMonth(now), endOfMonth(now));
    weekProgress = calculateProgress(
      startOfWeek(now, { weekStartsOn: 6 }), // 6 represents Saturday
      endOfWeek(now, { weekStartsOn: 6 })
    );
    dayProgress = calculateProgress(startOfDay(now), endOfDay(now));

    //console.log(yearProgress, monthProgress,  weekProgress, dayProgress);

    // Calculate life progress if dates exist in localStorage
    const startDateStr = localStorage.getItem('startDate');
    const endDateStr = localStorage.getItem('endDate');
    const savedTitle = localStorage.getItem('title');

    if (startDateStr && endDateStr && savedTitle) {
      const startDate = parse(startDateStr, 'dd/MM/yyyy', new Date());
      const endDate = parse(endDateStr, 'dd/MM/yyyy', new Date());
      lifeProgress = calculateProgress(startDate, endDate);
      title = savedTitle;
      //console.log('LifeProgress: ', lifeProgress);
    }
  }

  function handleShowHideModal(event: KeyboardEvent) {
    console.log(event);
    if (event.ctrlKey && event.key === 'e') {
      showModal = true;
      event.preventDefault();
    }
  }

  onMount(() => {
    updateProgress();
    const interval = setInterval(updateProgress, 1000);
    window.addEventListener('keydown', handleShowHideModal);

    showModal = !modal.handleCancel();

    return () => {
      clearInterval(interval);
      window.removeEventListener('keydown', handleShowHideModal);
    };
  });
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
    <div class="engrave-holder">
      <h2 class="engrave">Press 'ctrl+e' to edit</h2>
    </div>
    <Modal bind:showModal bind:this={modal}/>
    
</main>


  <style>
    main {
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      background: #f0f0f0;
      padding: 1rem;
      flex-direction: column;
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
        color: #4a90e2;
    }
  
    @media (max-width: 600px) {
      .progress-container {
        padding: 1rem;
      }
  
      h1 {
        font-size: 1.5rem;
      }
    }

    .engrave-holder{
      margin-top: 3rem;
    }

    .engrave, .quote::before {
      background: #b1b1b1;
      -webkit-background-clip: text;
              background-clip: text;
      color: transparent;
      text-shadow: rgba(255, 255, 255, 0.5) 1px 2px 1px;
    }

  </style>