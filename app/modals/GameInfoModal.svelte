<script>
    import { onMount } from 'svelte'
    import { closeModal } from 'svelte-native'
    import Genre from '../components/Genre.svelte'
    export let result

    let gameInfo
    let platforms
    let iteratedPlatforms = []
    let publishers
    let iteratedPublishers = []
    let genres
    let iteratedGenres = []
    let esrbAcro

    const getGameInfo = () => {
        let id = result.id
        const url = `https://rawg-video-games-database.p.rapidapi.com/games/${id}`
        fetch(url, {
            "headers": {
                "x-rapidapi-host": "rawg-video-games-database.p.rapidapi.com",
        		"x-rapidapi-key": "f9a3752d54msh9bf32664688ad4dp1c651ejsn49d236f5b883"
        	}
        })
        .then( response => response.json() )
        .then( json => {
            gameInfo = json
            platforms = gameInfo.parent_platforms
            publishers = gameInfo.publishers
            genres = gameInfo.genres
            getMiscData()
        })
    } 

    const getMiscData = () => {
        // Iterer gjennom ulike platform som spillet kan spilles på.
        let i 
        for(i = 0; i < platforms.length; i++){
            iteratedPlatforms.push(platforms[i].platform.slug)
        }
        for(i = 0; i < publishers.length; i++){
            iteratedPublishers.push(publishers[i].name)
        }
        for(i = 0; i < genres.length; i++){
            iteratedGenres.push(genres[i].name)
        }
        let esrbRating = gameInfo.esrb_rating.name
        if(esrbRating == "Everyone"){
            esrbAcro = "E"
        }else if(esrbRating == "Everyone 10+"){
            esrbAcro = "E10"
        }else if(esrbRating == "Teen"){
            esrbAcro = "T"
        }else if(esrbRating == "Mature"){
            esrbAcro = "M"
        }else if(esrbRating == "Adults Only"){
            esrbAcro = "AO"
        }else{
            esrbAcro = "RP"
        }
    }

    onMount(() => {
        getGameInfo()
    })
    

</script>

<frame>
    <page>
        {#if gameInfo}
            <actionBar title='{gameInfo.name}' />
            <scrollView>
               <stackLayout style='padding:10'>
                    <image src='{gameInfo.background_image}' alt="cover Image" />
                    <flexboxLayout justifyContent="space-between">
                        <label class="h5" text="Release date: {gameInfo.released} " />
                        <label class="h5" text="{iteratedPublishers[0]}" />
                    </flexboxLayout>
                    <wrapLayout class="genresContainer">
                        {#each iteratedGenres as genre}
                            <label class="h3 genreText" text="{genre}"/>}
                            <label text="  "/>
                        {/each}
                    </wrapLayout>
                    <flexboxLayout class="miscInfoContainer">
                        {#if gameInfo.metacritic}
                            <stackLayout> 
                                <label class="h3 centered" text="Metascore" />
                                <label class="h1 metacritic"  style="background-color:rgba(0, 116, 10, 0.637)" text='{gameInfo.metacritic}' />
                            </stackLayout>
                        {:else}
                            <label text="" style="display:none"/>
                        {/if}
                        <stackLayout>
                            <label class="h3 centered" text="Age rating" />
                            <image class="esrbPicture" src="~/image/{esrbAcro}.png"/>
                        </stackLayout>
                        <stackLayout>
                            <label class="h3" text="Platforms" />
                            <wrapLayout width="60">
                                {#each iteratedPlatforms as platform}
                                    <image src="~/image/{platform}.png" width="20" height="20"/>
                                {/each}
                            </wrapLayout>
                        </stackLayout>
                    </flexboxLayout>    
                    <stackLayout class="gameDescriptionContainer">
                        <htmlView class="gameDescription" html="{gameInfo.description}"  />
                    </stackLayout>
                </stackLayout>
            </scrollView>
        {:else}
            <activityIndicator busy={true} />
            <label text="Loading..." />
        {/if}
    </page>
</frame>

<style>
    .centered{
        text-align: center;
    }
    .genresContainer{
        width: 100%;
        height: 100%;
        margin-top: 8;
        margin-bottom: 8;
        border-bottom-width: 1;
        border-bottom-color: rgba( 53, 53, 53, 0.3);
        padding-top: 10;
        padding-bottom: 10;
        align-items: center;
    }
    .genreText{
        padding: 5;
        border-width: 1;
    }
    .miscInfoContainer{
        margin: 1 0;
        justify-content: space-around;
        flex-wrap: wrap;
    }
    .metacritic{
        width: 50;
        padding: 3 5;
        border-width: 1;
        border-radius: 5;
        flex: 2;
        text-align: center;
    }
    .esrbPicture{
        width: 50;
        height: 50;
    }
    .gameDescriptionContainer{
        width: 100%;
        margin-top: 10;
    }
    .gameDescription{
        width: 250;
        margin-top: 10;
        margin-left: 3;
        padding-top: 15;
        padding-left: 10;
        border-left-width: 2;
        font-size: 15;
    }
</style>