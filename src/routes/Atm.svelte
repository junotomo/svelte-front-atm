<script>
    import { onMount ,afterUpdate} from 'svelte'
    import Button, { Label } from '@smui/button';
    import Note from './note.svelte'
    import MoneyCounter from "./moneyCounter.svelte";

    
    let withdraw = {}
    let amount = ""
    let cash = {}
 let data = {}

    const getAtmData = async () => {
        try {
            const response = await fetch("http://localhost:3000/atm/atmData")
            const fetchObj = await response.json()
            cash = fetchObj["number of notes"]
        } catch (error) {
            console.log(error)
        }

    }

    const getBalance = async () => {
        try {
            const response = await fetch("http://localhost:3000/atm/balance")
            data = await response.json()
        } catch (error) {
            console.log(error)
        }

    }

    // @ts-ignore
    const withdrawMoney = async ()=> {
        try {
           

            const response = await fetch("http://localhost:3000/atm/withdrawal", {
                    method: "PUT",
                    headers: {
                        "Content-Type": "application/json",
                    },
                    body: JSON.stringify({
                        pin: 777,
                        amount: amount
                    })
                })
            withdraw = await response.json()
            getBalance()
            getAtmData()
        } catch (error) {
            console.log(error)
            alert("Valor inválido")
        }
    } 


</script>

<div class="atm-interface">

    <div class="screen">
        <h1>Saldo atual :</h1>
        <h2>$:{data.balance} </h2>
    </div>

    <div class="form">
        <input type="number" bind:value={amount}>
    </div>

    <div class="interface-btn">
        <Button on:click={() => withdrawMoney()} 
            on:click={() => getBalance()}
            on:click={() => getAtmData()}
            
            touch variant="raised">
            <Label>Saque</Label>
        </Button>
    </div>

    <div class="withdraw-money">
        {#each Object.entries(withdraw) as [key,value]}
            <Note quantity={value} notetype={key}/>
         {/each}
    </div>

</div>
<MoneyCounter atmData={cash}/>

