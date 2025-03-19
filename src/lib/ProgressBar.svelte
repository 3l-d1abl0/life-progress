<script lang="ts">

    let {label, percentage, ...props } = $props();
    //$inspect(props);


</script>
  
  <div class="progress-container">
    <div class="progress-label">{label}: {percentage.toFixed(2)}%</div>
    <div class="progress-bar">
        <div class="progress-fill" style="width: {percentage}%"></div>
    </div>
  </div>
  
  <style>
    .progress-container {
      margin: 1rem 0;
      width: 100%;
    }
  
    .progress-label {
      margin-bottom: 0.5rem;
      color: #666;
      font-size: 0.9rem;
    }
  
    .progress-bar {
      height: 20px;
      background: #e0e0e0;
      border-radius: 10px;
      box-shadow: inset 3px 3px 6px rgba(0, 0, 0, 0.1),
                  inset -3px -3px 6px rgba(255, 255, 255, 0.7);
      overflow: hidden;
    }
  
    .progress-fill {
      height: 100%;
      background: linear-gradient(45deg, #4a90e2, #63b3ed);
      border-radius: 10px;
      transition: width 0.3s ease;
      box-shadow: 2px 0 4px rgba(0, 0, 0, 0.2);
    }
  </style>