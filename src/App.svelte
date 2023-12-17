<script>
  import { fade,fly, slide } from "svelte/transition";
  import gsap from "gsap"
  let playerInp
  let playerArr = []
  let isPlayersShown = false 
  let randomArr = []
  let randomBtn
  let hasReversed = false
  let hasMovedToNext = false

  function addPlayer(){
      let newPlayer = playerInp.value
      playerInp.value = ''
      playerArr = [...playerArr, {playerName: newPlayer} ]
      isPlayersShown = true

      randomArr = []

  }

  function removePlayer(e){
      let targetedPlayer = e.target.previousElementSibling.textContent
      console.log(targetedPlayer)
      playerArr = playerArr.filter((obj)=>{
            return !targetedPlayer.includes(obj.playerName)
      } )
      playerArr = [...playerArr]
      console.log(playerArr)

      if(playerArr.length === 0){
        isPlayersShown = false
      }


      randomArr = []
  }

  function random(){
    randomArr = []
    let num 
    randomBtn.disabled =true
    randomBtn.textContent = 'randomising...'
    let myInterval = setInterval(() => {
  num = Math.floor(Math.random() * playerArr.length ) + 1

      while(randomArr.includes(num)){
      num = Math.floor(Math.random() * playerArr.length ) + 1
    }
    randomArr = [...randomArr, num]

  if(randomArr.length === playerArr.length){
    clearInterval(myInterval)
    randomBtn.disabled = false
    randomBtn.textContent = 'Randomise'

  }

}, 800);

}



function nextSection(){

  if(playerArr.length === 0 || playerArr.length === 1){
    alert('Please input more than one player')
    return
  }
  let tl_one = gsap.timeline()
tl_one.to('.next-btn', {
  x: -200,
  opacity: 0,
}).to('.player-boxes', {
    x: -300,
    opacity: 0,
    stagger: 0.2,
    ease: 'power1.inOut'
  })
    .to('.player-container', {
      y: -100,
      duration: 0.2,
      opacity: 0,
    })
  .to('.input-box', {
    y: -200,
    opacity: 0,
    ease: 'power3.inOut'
  }).to('.who-players',{
    y:-100,
    opacity: 0,
    duration: 0.2
  }).to('.heading',{
    y:-100,
    opacity: 0,
    duration: 0.2,
    onComplete: ()=>{hasMovedToNext = true},
    
  }).fromTo('.vs-list',{
    y: -200,
    opacity: 0,
    
  }, {
    opacity: 1,
    y: 0,
    onReverseComplete: ()=>{hasMovedToNext = false},

    duration: 0.3
  })

  // if(hasReversed){
  //   tl_one.play()
  // }
}

function reverse(){
  hasReversed = true
  gsap.timeline().fromTo('.vs-list',{
    y: 0,
    opacity: 1,
    
  }, {
    opacity: 0,
    y: -200,
    onComplete: ()=>{hasMovedToNext = false},
    duration: 0.3
  }).to('.heading',{
    y:0,
    opacity: 1,
    duration: 0.2,
   
    
  }).to('.who-players',{
    y:0,
    opacity: 1,
    duration: 0.2
  }).to('.input-box', {
    y: 0,
    opacity: 1,
    ease: 'power3.inOut'
  })   .to('.player-container', {
      y: 0,
      duration: 0.2,
      opacity: 1,
    }).to('.player-boxes', {
    x: 0,
    opacity: 1,
    stagger: 0.2,
    ease: 'power1.inOut'
  }).to('.next-btn', {
  x: 0,
  opacity: 1,
})

}

</script>

<main class="flex flex-col items-center justify-center min-h-[100vh] gap-10 max-w-[500px] mx-auto">

<div class={`${hasMovedToNext ? 'hidden' : 'flex'} flex-col items-center justify-center gap-6`}>
  <div class=" flex flex-col gap-4 text-center items-center">
    <h1 class="heading text-4xl">Who's Next?</h1>

    <p class="who-players">Who are the players?</p>

    <div class="input-box">

      <input bind:this={playerInp} type="text" class="border-2 border-black px-4 py-2 rounded-md">

      <button on:click={addPlayer} class="border-2 border-black rounded-md p-2">click</button>
    </div>
  </div>


  <!-- Section that displays the listed players -->
  <div class= {`player-container ${isPlayersShown && "flex flex-col border-2 border-black rounded-md w-full "}`}>

    {#each playerArr as player}

      <div transition:slide class="player-boxes flex gap-2 p-4 items-center justify-between">
        <div class="flex">
          <p class="mr-2">{playerArr.indexOf(player) + 1}.</p>
          <p>{player.playerName}</p>
        </div>
        <button  on:click={removePlayer} class="border-black border-2 rounded-[50%] p-2">✖</button>
      </div>
      
    {/each}


  </div>

  <button on:click={nextSection} class="next-btn border-black border-2 p-2">
    Next
  </button>
</div>


  <!-- This is the next section that displays the players and randomised box -->
<div class={`vs-list ${hasMovedToNext ? 'flex' : 'hidden'} flex flex-col gap-4`}>

  <div class="flex gap-2">
    <p>The players are</p>

    <div>
      {#each playerArr as player}
       
        {#if playerArr.at(-1) === player}
        {player.playerName}
        {:else}
        {player.playerName},  
        {/if}
      {/each}
    </div>
  </div>

  <button bind:this={randomBtn} class="border-black border-2 p-2" on:click={random}>
    randomise
  </button>




  <div class=" grid grid-cols-2 grid-flow-row justify-center items-center w-[70%] mx-auto">

    {#each randomArr as number}
      <div transition:slide class="flex  justify-between">
       <p class={`${(randomArr.indexOf(number) + 1 ) % 2 === 0 && 'ml-auto'}`}> {playerArr[number-1].playerName}</p>

        {#if (randomArr.indexOf(number) + 1 ) % 2 === 1 && randomArr.indexOf(number) !== playerArr.length}
            <p>vs</p>
        {/if}
      </div>
    {/each}
  </div>  

  <button class=" reverse-btn border-black border-2 p-2" on:click={reverse}>
    ← back
  </button>
</div>

</main>

<style>

</style>
