<script lang="ts">
  import Tile from './Tile.svelte';
  let name: string
  enum STATE {
      NEW,
      RUNNING,
      PAUSED,
  }
  
  let state: STATE = STATE.NEW;
  let startTime: number = 0;
  let elaspedTime: number = 0;
  let oldElapsedTime: number = 0;
  let interval: any;
  let gameStatus: Boolean = false;
  let randomColor = () => Math.floor(Math.random() * 16777215).toString(16);
  
  const pad2 = (number: number) => `00${number}`.slice(-2);
  const pad3 = (number: number) => `00${number}`.slice(-2);
  let Game:{
      id: number;
      status: boolean;
  }
  let Squares_list: {
      value: string;status: boolean;found: boolean
  } [] = [];
  let Rank: {
      id: number;
      color: string;
      place: number;
      time: any;
  } [] = [];
  Rank = [{id: 0, color: '252543', place: 1, time: '00:00:19.67'},{id: 1, color: '170e3a', place: 2, time: '00:00:10.67'},
  {id: 2, color: '88715d', place: 3, time: '00:00:11.09'},{id: 3, color: '56d0d3', place: 4, time: '00:00:15.25'}]
  let Board: {
      title: string;
      status: boolean;
  } [] = []
  Board = [{title:'3x4',status:true},{title:'4x4',status:false},{title:'4x5',status:false}]
  let addToList = () => {
      Rank = [...Rank, {id: Rank.length,
          color: randomColor(),
          place: Rank.length==0?1:Rank[Rank.length-1].place+1,
          time: formattedElaspedTime,
      }];
  }
  
  $: hours = pad2(Math.floor(elaspedTime / 1000 / 60 / 60) % 60);
  $: minutes = pad2(Math.floor(elaspedTime / 1000 / 60) % 60);
  $: seconds = pad2(Math.floor(elaspedTime / 1000) % 60);
  $: millis = pad3(elaspedTime % 1000);
  $: formattedElaspedTime = `${hours}:${minutes}:${seconds}.${millis}`;
  
  
  
  function add_elem(icons) {
      for (let i = 0; i < icons.length; i++)
          for (let x = 0; x <= 1; x++)
              Squares_list = [...Squares_list,{
                  value: icons[i],
                  status: false,
                  found: false
              }]
              /*                Squares_list.push({
                  value: icons[i],
                  status: false,
                  found: false
              });*/
  }
  add_elem(["🎅", "❄️", "🎁", "🎄", "✨", "🛷"]);

  console.log(Squares_list);
  const Squares_listCopy = [...Squares_list]
  function shuffle(array): void {
      let currentIndex = array.length,
          randomIndex;
  
      // While there remain elements to shuffle.
      while (currentIndex != 0) {
  
          // Pick a remaining element.
          randomIndex = Math.floor(Math.random() * currentIndex);
          currentIndex--;
  
          // And swap it with the current element.
          [array[currentIndex], array[randomIndex]] = [
              array[randomIndex], array[currentIndex]
          ];
      }
  
      return array;
  }
  const start = () => {
      startTime = Date.now();
      state = STATE.RUNNING;
      interval = setInterval(() => {
          if (state === STATE.RUNNING) {
              const endTime = Date.now();
              elaspedTime = endTime - startTime + oldElapsedTime;
          }
      });
  };
  const place = (key) => {
      switch (key) {
          case 1:
              return '🏆 - '
          case 2:
              return '😎 - '
          case 3:
              return '💩 - '
          default:
              break;
      }
      return ''
  }
  shuffle(Squares_list)
  
  $: if(Squares_list.every(item=>item.found == true)){
      const reset = () => {
          elaspedTime = 0;
          state = STATE.NEW;
          clearInterval(interval);
      };
      addToList();
      gameStatus = !gameStatus
      Squares_list.filter(item=>{item.found=false,item.status = false})
      shuffle(Squares_list);
      reset()
  };
  const handleClick = (i) => {
      if (!gameStatus) {
          start()
          gameStatus = !gameStatus
      }
      Squares_list[i].status = !Squares_list[i].status
      let items_active = Squares_list.filter(item => item.status == true);
      if (items_active.length === 2 && items_active[0].value === items_active[1].value) {
          items_active.forEach(element => element.found = true)
      }
      if (items_active.length > 2) {
          Squares_list[i].status = !Squares_list[i].status
          items_active.filter(item => {
              item.status = item.status ? false : true
          });
      }
  }
  const changeBoard = (i) => {
      Board.forEach(element => {
          element.status=false
      });
      Board[i].status= true
      switch (i) {
          case 0:
              Squares_list = Squares_listCopy;
              shuffle(Squares_list);
              break;
          case 1:
              Squares_list = Squares_listCopy;
              add_elem(["☃️", "🔔"]);
              shuffle(Squares_list);
              break;
          case 2:
              Squares_list = Squares_listCopy;
              add_elem(["☃️", "🔔","🦌","🧝"]);
              shuffle(Squares_list);
              break;
      }
  }
    
  </script>
  <div class="grid h-screen place-items-center">
      
      <div class="grid overflow-hidden auto-cols-auto auto-rows-auto gap-2 grid-flow-row">
          <div class="min-w-full min-h-full row-span-1 col-start-1 sm:col-span-2 ">
              <p class="text-xl mb-2">Ustaw plansze:</p>
              {#each Board as button,id}
              <button class="{button.status?'btn btn-active btn-primary mx-1':'btn btn-outline btn-primary mx-1'}" on:click={()=>changeBoard(id)}>{button.title}</button>
              {/each}
          </div>
          <div class="min-w-full min-h-full row-span-3 bg-red-500 rounded">
              <div class="{Board[0].status?'grid grid-cols-3 gap-2 p-2':'grid grid-cols-4 gap-2 p-2'}" >
              {#each Squares_list as tile, id}
              <Tile value={tile.status || tile.found?tile.value:'?'} color={tile.found?'solved':''} on:click={()=>handleClick(id)}/>
              {/each}
              </div>
          </div>
          <div class="min-w-full min-h-full grid gap-2 md:col-start-2 sm:col-start-1 col-span-1 row-span-3">
                  <div class="bg-red-500 text-xl font-semibold p-2 px-7 grid row-span-1 rounded place-items-center place-content-center">
                      <p>{formattedElaspedTime}</p>
                  </div>
                  <div class="bg-red-500 p-4 px-7 grid row-span-12 rounded">
                      {#each Rank as player}
                          <p>{place(player.place)}{player.place} - {player.time}</p>
                      {/each}
                  </div>
          </div>
      </div>
  </div>
  
  <style>
  </style>
  