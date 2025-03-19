<script lang="ts">
  import { isValid, parse } from 'date-fns';
  
  let { showModal = $bindable() } = $props();
  //export let showModal;

  let startDate = '';
  let endDate = '';
  let title = '';
  let error = $state('');


  // Check for common XSS patterns
  function isValidTitle(str: string): boolean {
    const xssPatterns = [
      /<script/i,
      /javascript:/i,
      /onerror=/i,
      /onload=/i,
      /onclick=/i,
      /alert\(/i,
      /eval\(/i,
      /document\./i,
      /\\/i,
      /<img/i,
      /<svg/i,
      /<iframe/i
    ];

    return !xssPatterns.some(pattern => pattern.test(str));
  }

  function validateDate(dateStr: string): boolean {
    const parsed = parse(dateStr, 'dd/MM/yyyy', new Date());
    return isValid(parsed);
  }

  function validateTitle(title: string): string{

    //Check for Blank Titles
    if (!title.trim()) {
      return 'Please enter a title';
      
    }
    
    //check Title Length
    if (title.length > 100) {
      return 'Title must be 100 characters or less';
      
    }

    //Check Title String
    if (!isValidTitle(title)) {
      return 'Invalid title. Please remove special characters or HTML';
      
    }

    console.log('Valid Title !');
    return '';
  }

  function handleSave() {

    //Check title
    error = validateTitle(title);
    if(error != '')
        return;

    //Check if start date is valid
    if (!validateDate(startDate)){
        error = 'Please enter Start Date in dd/mm/yyyy format';
        console.log('Invalid Start Date !');
        return;  
    }

    //Check if end Date is valid
    if (!validateDate(endDate)) {
      error = 'Please enter End Date in dd/mm/yyyy format';
      console.log('Invalid End Date');
      return;
    }

    const start = parse(startDate, 'dd/MM/yyyy', new Date());
    const end = parse(endDate, 'dd/MM/yyyy', new Date());
    const now = new Date();

    if (start >= now) {
      error = 'Start date must be in the past';
      return;
    }

    if (end <= now) {
      error = 'End date must be in the future';
      return;
    }

    //Save it to local Storage
    localStorage.setItem('startDate', startDate);
    localStorage.setItem('endDate', endDate);
    localStorage.setItem('title', title);

    error ='';//remove error message
    console.log('Saving');
    showModal = false;//hide the box
  }


  function handleCancel() {

    const savedStart = localStorage.getItem('startDate');
    const savedEnd = localStorage.getItem('endDate');
    const savedTitle = localStorage.getItem('title');

    //if everyting is valid, hide modal
    if (savedStart && savedEnd && savedTitle && validateDate(savedStart) && validateDate(savedEnd) && validateTitle(savedTitle)=='') {
      console.log(savedStart, savedEnd, savedTitle);
      showModal = false;
      error = '';
    } else {
      error = 'Please enter valid dates and title before closing';
    }
  }


</script>

{#if showModal}
<div class="modal-overlay">
  <div class="modal">
    <h2>Set Progress Details</h2>
    
    {#if error}
      <div class="error">{error}</div>
    {/if}

    <div class="input-group">
      <label for="title">Title:</label>
      <input 
        type="text" 
        id="title"
        bind:value={title}
        placeholder="Enter title (max 100 characters)"
        maxlength="100"
      />
    </div>

    <div class="input-group">
      <label for="startDate">Start Date (dd/mm/yyyy):</label>
      <input 
        type="text" 
        id="startDate"
        bind:value={startDate}
        placeholder="dd/mm/yyyy"
      />
    </div>

    <div class="input-group">
      <label for="endDate">End Date (dd/mm/yyyy):</label>
      <input 
        type="text" 
        id="endDate"
        bind:value={endDate}
        placeholder="dd/mm/yyyy"
      />
    </div>

    <div class="button-group">
        <button onclick={handleSave}>Save</button>
        <button onclick={handleCancel}>Cancel</button>
    </div>
  </div>
</div>
{/if}


<style>
    .modal-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.5);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }
  
    .modal {
      background: #f0f0f0;
      padding: 2rem;
      border-radius: 20px;
      box-shadow: 20px 20px 60px #424242,
                  -20px -20px 60px #424242;
      width: 90%;
      max-width: 500px;
    }
  
    .input-group {
      margin: 1rem 0;
    }
  
    label {
      display: block;
      margin-bottom: 0.5rem;
      color: #666;
    }
  
    input {
      width: 100%;
      padding: 0.5rem;
      border: none;
      border-radius: 10px;
      background: #e0e0e0;
      box-shadow: inset 2px 2px 5px #babecc,
                  inset -5px -5px 10px #ffffff;
    }
  
    .button-group {
      display: flex;
      justify-content: flex-end;
      gap: 1rem;
      margin-top: 2rem;
    }
  
    button {
      padding: 0.5rem 1rem;
      border: none;
      border-radius: 10px;
      background: #e0e0e0;
      box-shadow: 5px 5px 10px #bebebe,
                  -5px -5px 10px #ffffff;
      cursor: pointer;
      transition: all 0.2s ease;
    }
  
    button:hover {
      box-shadow: 2px 2px 5px #bebebe,
                  -2px -2px 5px #ffffff;
    }
  
    .error {
      color: #e53e3e;
      margin-bottom: 1rem;
      padding: 0.5rem;
      border-radius: 5px;
      background: rgba(229, 62, 62, 0.1);
    }
  </style>