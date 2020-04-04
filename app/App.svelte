<script>
    import { onMount } from 'svelte'
    import { showModal } from 'svelte-native'
    import { Template } from 'svelte-native/components'
    import GameInfoModal from './modals/GameInfoModal.svelte'

    let results = []
    let savedResults = []
    let pageNumb = 1
    let searchbar = false
    let abTitle = "" 
    let searchValue = ""
    let url = `https://rawg-video-games-database.p.rapidapi.com/games?page=${pageNumb}&page_size=15&search=${searchValue}`
    $: url = `https://rawg-video-games-database.p.rapidapi.com/games?page=${pageNumb}&page_size=15&search=${searchValue}`
    
    const getData = () => {
        fetch(url = `https://rawg-video-games-database.p.rapidapi.com/games?page=${pageNumb}&page_size=15&search=${searchValue}`, {
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

    const nextPage = () => {
        pageNumb++
        getData()
        results = []
    }
    const prevPage = () => {
        pageNumb--
        console.log(url)
        getData()
        console.log(url)
        results = []
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
    const showSearchBar = () => {
        searchbar = !searchbar
        console.log(searchValue)
    }
</script>

<page>
    <actionBar title={abTitle}>
        {#if searchbar}
            <actionItem>
                <searchBar 
                    hint="Search game title" 
                    on:clear={showSearchBar}
                    bind:text="{searchValue}"
                    on:submit={() => {
                        showSearchBar()
                        getData()
                        results = []
                        pageNumb = 1
                    }}
                />
            </actionItem>
        {:else}
            <actionItem android.systemIcon="ic_menu_search" android.position="actionBar" on:tap={showSearchBar} />
            {abTitle = "Game Catalog"}
            <actionItem android.systemIcon="ic_menu_rotate" android.position="actionBar" 
                on:tap={() => {
                    if(searchValue != "" || pageNumb != 1){
                        results = []
                        searchValue = ""
                        pageNumb = 1
                        getData()
                    }else{
                        return;
                    }
                }} />
        {/if}
    </actionBar>
    <stackLayout class="main">
        <flexboxLayout class="btnContainer" >
            {#if pageNumb > 1}
                <button text="previous" on:tap={prevPage}/>
                <label class="h2" text="page {pageNumb}"/>
                <button text="next" on:tap={nextPage}/>
            {:else}
                <button text="previous" style="visibility:hidden"/>
                <label class="h2" text="page {pageNumb}"/>
                <button text="next" on:tap={nextPage}/>
            {/if}
        </flexboxLayout>
        <listView items={results} width="100%" height="100%" class="gameContainer">
            <Template let:item={result}>
                <image class="gameImage" src="{result.background_image}"on:tap={showGameInfo(result)} />
                <absoluteLayout>
	        	    <label class="gameTitle h3" text="{result.name}" on:tap={showGameInfo(result)} />
                    <flexboxLayout flex-direction="row-reverse" class="iconContainer">
                        {#each result.parent_platforms as platform}
                            <image src="~/image/{platform.platform.slug}.png" class="icons" />
                        {/each} 
                    </flexboxLayout>
                </absoluteLayout>
	        </Template>
        </listView>
    </stackLayout>
</page>

<style>
    .main{
        margin: 0 25 0 25;
    }
    .gameImage{
        width: 100%;
        border-top-width: 1;
    }
    .iconContainer{
        width: 100%;
        height: 38;
        margin: 0;
        top: 20;
        justify-content: center;
        align-items: center;
    }
    .icons{
        width: 20;
        height: 20;
    }
    .gameTitle{
        width: 100%;
        height: 50;
        margin: 0;
        border-width: 1;
        border-top-color: rgba(133, 133, 133);
        background-color: rgba(133, 133, 133, 0.8);
        color: black;
        text-align: center;
        font-size: 18;
    }
    .btnContainer{
        width: 100%;
        justify-content: space-between;
        align-items: center;
    }

</style>

