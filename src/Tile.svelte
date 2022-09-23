<script lang="ts">
    export let value;
    export let color;
  </script>
  
  <style>
    @keyframes example {
      from {background-color: rgb(255, 255, 255);}
        to {background-color: rgb(40, 51, 206);}
    }
    .x {
      flex: 1 0 25%;
      width: 50px;
      height: 80px;
      background-color: whitesmoke;
      border: 2px solid black;
      margin: 5px;
      padding: 5px;
      font-size: 20px;
      text-align: center;
    }
  
    .x:hover {
      border: 2px solid red;
      background-color: black;
      color: white;
      border-color: white;
    }
    .solved{
      background-color: blue;
      animation-name: example;
      animation-duration: 1s;
    }
    
  </style>
  
  <button class="{color} btn text-2xl px-14 py-8 content-center max-w-0" style="" on:click>{value}</button>