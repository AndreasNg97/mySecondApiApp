<script>
    import { onMount } from 'svelte'
    import { showModal } from 'svelte-native'
    import { Template } from 'svelte-native/components'
    import GameInfoModal from './modals/GameInfoModal.svelte'

    let results = []
    let savedResults = []
    let pageNumb = 1
    let pageSize = 20
    let searchbar = false
    let abTitle = "" 
    let searchValue = ""
    let url = `https://rawg-video-games-database.p.rapidapi.com/games?page=${pageNumb}&page_size=${pageSize}&search=${searchValue}`
    $: url = `https://rawg-video-games-database.p.rapidapi.com/games?page=${pageNumb}&page_size=${pageSize}&search=${searchValue}`
    const getData = () => {
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

/*     const test2 = async(json) => {
        for(let i = 0; i < json.results.length; i++){
            await results.push(json.results[i])
            console.log(i, "-", results[i].name)
        }
    } */

    const nextPage = () => {
        pageSize += 10
        console.log(pageSize)
        getData()
        console.log(url)
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

    const test = () => {
        console.log(results)
        if(savedResults == results) {
            console.log("yes")
        }else{
            console.log("No")
        }
    }

</script>

<page>
    <actionBar 
        title={abTitle}
        android.icon="res://icon"
        android.iconVisibility="always" 
        >
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
                {abTitle = "Game Catalogg"}
            {/if}
    </actionBar>
    <stackLayout class="main" >
        <flexboxLayout class="btnContainer" >
            {#if pageNumb > 1}
            <button text="previous" />
            <label class="h2" text="page {pageNumb}" />
            <button text="next"/>
            {:else}
            <button text="previous" style="visibility:hidden"/>
            <label class="h2" text="page {pageNumb}" />
            <button text="next" />
            {/if}
        </flexboxLayout>
        <listView items={results} width="100%" height="100%" class="gameContainer">
            <Template let:item={result}>
                <absoluteLayout>
	        	    <label class="gameTitle h3" text="{result.name}" on:tap={showGameInfo(result)} />
                    <flexboxLayout flex-direction="row-reverse" class="iconContainer">
                        {#each result.parent_platforms as platform}
                                <image src="~/image/{platform.platform.slug}.png" class="icons" />
                        {/each} 
                    </flexboxLayout>
                </absoluteLayout>
                <image class="gameBackground" src="{result.background_image}"on:tap={showGameInfo(result)} />
	        </Template>
        </listView>
    </stackLayout>
</page>

<style>
    .main{
        margin: 0 25 50 25;
    }
    .gameContainer{
        margin-top: 0;
    }
    .gameBackground{
        width: 100%;
    }
    .iconContainer{
        width: 100%;
        height: 38;
        margin: 0;
        justify-content: flex-end;
    }
    .icons{
        width: 20;
        height: 20;
    }
    .gameTitle{
        text-align: left;
        margin: 0;
        background-color: rgba(158, 158, 158);
        color: black;
        height: 38;
        width: 100%;
    }
    .btnContainer{
        width: 100%;
        justify-content: space-between;
        align-items: center;
    }

</style>

