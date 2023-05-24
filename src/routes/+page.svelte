<script>
  import lucaspaz from '../assets/img/lucas-peace.png';
  import lucastilt from '../assets/img/pissedoff.png';
  import { onMount } from 'svelte';
  import { collection, getDocs,doc, setDoc } from "firebase/firestore";
  import { db } from '../routes/+layout.svelte';
  
  
  let image = lucaspaz
  let tiltedRecently = false

  $: tilt = 0

  onMount(async () => {
    verifyIfTilted()
    setInterval(async () => {
      await verifyIfTilted()
    }, 60000);
  })

  async function verifyIfTilted(){
    let last_tilt = ''

    try {
      let docRef = await getDocs(collection(db, "data"));
      docRef.forEach((doc) => {
        doc.data().last_time_tilted ? last_tilt = doc.data().last_time_tilted : last_tilt = ''
        
        tilt = doc.data().tilt_count
        last_tilt = new Date(last_tilt.seconds * 1000 + last_tilt.nanoseconds / 1000000)
  
        let now = new Date()
        let diff = now - last_tilt
        if (diff < 300000) {
          tiltedRecently = true
        }else{
          tiltedRecently = false
        }
      });
      
        
    } catch (error) {
      console.error('Erro ao obter dados do Firestore:', error);
    }
  }

  async function canAddTilt(){
    let canTilt = true
    try {
      let docRef = await getDocs(collection(db, "data"));
      docRef.forEach((doc) => {
        doc.data().tilt_count > tilt ? canTilt = false : canTilt = true
      });
      
        return canTilt
    } catch (error) {
      console.error('Erro ao obter dados do Firestore:', error);
    }
  }

  async function postTilt(){
    if(canAddTilt()){
      try {
        const docRef = doc(db, "data", "uVWaQBObRq1PTKGJ09ku")
        await setDoc(docRef, {
          tilt_count: tilt,
          last_time_tilted: new Date()
        });
        console.log("Document written with ID: ", docRef.id);
      } catch (e) {
        console.error("Error adding document: ", e);
      }
    }
    verifyIfTilted()
  }

  function tiltou(){
    document.getElementById('container').classList.add('container-content')
    tilt += 1
    image = lucastilt
    postTilt()
    setTimeout(() => {
      image = lucaspaz
      document.getElementById('container').classList.remove('container-content')
    }, 3000);
  }
</script>

<div id="container" class="bg-orange-500 flex items-center justify-between flex-col w-screen h-screen">
  <div>
    <h1 class="text-9xl text-center text-white">Ultimate Lucas Tilt Counter</h1>
    <div class="flex flex-col items-center">
      <p class="text-3xl">O Lucas tiltou hoje ?</p>
      <button disabled={tiltedRecently} id="button" on:click={() => tiltou()} class="p-2 mt-2 hover:bg-gray-700 
        disabled:bg-gray-100 
        disabled:text-gray-300 
        bg-black text-white">Tiltou</button>
    </div>
  </div>

  <div class="flex flex-col items-center justify-end">
    <div>
      {#if tilt == 1}
        <p class="text-3xl text-white">Tiltou {tilt} vez</p>
      {:else if tilt > 1}
        <p class="text-3xl text-white">Tiltou {tilt} vezes</p>
      {:else}
        <p class="text-3xl text-white">Nao tiltou ainda</p>
      {/if}
    </div>
    <img src={image} alt="">
  </div>
</div>

<span class="container-content"></span>

<style>
  .container-content{
    background: linear-gradient(90deg, rgba(249,115,22,1) 0%, rgba(249,52,22,1) 36%, rgba(249,52,22,1) 59%, rgba(249,115,22,1) 100%);
  }
</style>