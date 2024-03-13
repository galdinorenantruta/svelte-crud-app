<script >
    import { initializeApp } from "firebase/app";
    import { getFirestore, collection, onSnapshot } from "firebase/firestore";
    import {firebaseConfig} from '$lib/firebaseconfig'
  

    const firebaseApp = initializeApp(firebaseConfig)

    const db = getFirestore()


    const collectionref = collection(db, "Todos")
    let todos = []

    const unsubscribe = onSnapshot(collectionref, (querySnapshot) =>{
        let fbtodos=[]
          querySnapshot.forEach((doc)=>{
            let todo={...doc.data(), id: doc.id}
            fbtodos = [todo, ...fbtodos] 
          })
          console.log(fbtodos)
    })

    
let error = ""
let task = ''
    
    const deleteTask = (index) => {
      let deleteItem  = todos[index]
     todos = todos.filter((item)=>item != deleteItem)
      
       
    }

    const addTask = () => {
        let todo = {
        task: task,
        isComplete: false,
        createdAt: new Date()
    }
    if(todo.task !=''){
        todos = [todo, ...todos]
    }else{error='Task is empty' }
        task = ''
    }

    
    $: console.log(todos)

    const markAsCompleted = (index) => {
        todos[index].isComplete = !todos[index].isComplete  
    }
    const keyUp = (event)=>{
        if(event.key === "Enter"){
            addTask()
        }
    }
</script>

<input type="text" bind:value={task} placeholder="add a Task" >
<button on:click={addTask}>Add</button> 

<ol>
    {#each todos as item, index}
        <li class:complete={item.isComplete}>
            <span>{item.task}</span>
            <span><button on:click={() => markAsCompleted(index)}>✔</button></span>
            <span><button on:click={() => deleteTask(index)}>❌</button></span>
        </li>
        {:else}
        <p>No tasks found</p>
        <p class="error">{error}</p>
        
    {/each}
</ol>
<svelte:window on:keyup={keyUp}/>

<style>
    .complete{
        text-decoration: line-through;
    }
    .error {
        color: red;
    }
</style>