<script>
    import { onMount } from 'svelte'
    import { showModal } from 'svelte-native'
    import GameInfoModal from './modals/GameInfoModal.svelte'

    let results = []
    let pageNumb = 1

    const getData = () => {
        const url = `https://rawg-video-games-database.p.rapidapi.com/games?page=${pageNumb}`
        fetch(url, {
        	"headers": {
        		"x-rapidapi-host": "rawg-video-games-database.p.rapidapi.com",
        		"x-rapidapi-key": "f9a3752d54msh9bf32664688ad4dp1c651ejsn49d236f5b883"
        	}
        })
        .then( response => response.json() )
        .then( json => {
            results = json.results
            })
        .catch(err => {
        	console.log(err);
        });
    }

    onMount(() => {
        getData();
    })

    const nextPage = async () => {
        pageNumb++
        getData()
    }
    const prevPage = async () => {
        pageNumb--
        getData()
    }

    const showGameInfo = async (result) => {
        await showModal(
            {
                page: GameInfoModal,
                props:{
                    result:result
                }
            }
        )
    }


</script>

<page>
    <actionBar title="Ghetto Game Catalog" />
    <stackLayout class="main" >
    <label class="h2" text="page {pageNumb}" style="text-alignment: center;" />
        <stackLayout orientation="horizontal" class="btnContainer">
            <button text='previous' on:tap='{prevPage}' />
            <button text='next' on:tap='{nextPage}' style="margin-left:100;"/>
        </stackLayout>
    
        <scrollView width="100%" height="100%">
            <stackLayout>
                {#each results as result}
                    <stackLayout 
                        class="gameContainer" 
                        style="background-image:url('{result.background_image}')"
                        on:tap={() => showGameInfo(result)}
                        >
                        <label class="gameTitle" text='{result.name}' />
                    </stackLayout>
                {:else}
                    <activityIndicator busy={true} />
                {/each}
            </stackLayout>
        </scrollView>
    </stackLayout>
</page>


<style>
    .main{
        margin: 25;
    }

    .gameContainer{
        background-size: cover;
        background-repeat: no-repeat;
        margin: 5;
        border-width: 1;
        height: 200;
    }

    .gameTitle{
        text-align: center;
        background-color: rgba(51, 51, 51, 0.7);
        color: #eee
    }

</style>
