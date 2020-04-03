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
    let gameDescriptionSwitch = true
    let gameDescriptionClass = "gameDescriptionClosed"
    let transBoo = "white"
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

    
    const switchClass = () => {
        if(gameDescriptionSwitch == false){
            gameDescriptionClass = "gameDescriptionClosed"
            transBoo = "white"
            gameDescriptionSwitch = true
        }else{
            gameDescriptionClass = "gameDescriptionOpen"
            transBoo = "transparent"
            gameDescriptionSwitch = false
        }
        console.log(iteratedGenres)
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
                    <label class="h5" text="Released: {gameInfo.released} " />
                    <!-- ------ -->
                    <flexboxLayout class="miscInfoContainer">
                        {#if gameInfo.metacritic}
                            <stackLayout class="metaContainer"> 
                                <label class="h3 centered" text="Metascore" />
                                <label class="h1 metacritic"  style="background-color:rgba(0, 116, 10, 0.637)" text='{gameInfo.metacritic}' />
                            </stackLayout>
                        {:else}
                                <absoluteLayout width="0" height="0 "></absoluteLayout>
                        {/if}
                        <stackLayout class="esrbContainer">
                            <label class="h3 centered" text="Age rating" />
                            <image class="esrbPicture" src="~/image/{esrbAcro}.png"/>
                        </stackLayout>
                        <stackLayout>
                            <label class="h3" text="Platforms" />
                            <wrapLayout class="platformContainer">
                                {#each iteratedPlatforms as platform}
                                    <image src="~/image/{platform}.png" width="20" height="20"/>
                                {/each}
                            </wrapLayout>
                        </stackLayout>
                        <stackLayout backgroundColor="blue">
                            <label class="h3" text="Publisher" />
                                {#each iteratedPublishers as publisher}
                                    <label text="{publisher}" />
                                {/each}
                        </stackLayout>
                        <stackLayout>
                            <label class="h3" text="Genres" />
                                {#each iteratedGenres as genre}
                                    <label text="{genre}" />
                                {/each}
                        </stackLayout>
                    </flexboxLayout>    
                    <!-- ------ -->
                    <absoluteLayout class="{gameDescriptionClass} gameDescriptionContainer" >
                        <absoluteLayout class="coverLayout" on:tap='{switchClass}'>
                            <label style="color:{transBoo}" class="coverLayoutText" text="Tap to read more" />
                        </absoluteLayout>
                        <htmlView class= "gameDescription" html="{gameInfo.description}" on:tap='{switchClass}' /> 
                    </absoluteLayout>    

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
    .miscInfoContainer{
        margin: 1 0;
        justify-content: space-between;
        flex-wrap: wrap;
    }
    .metacritic{
        text-align: center;
        width: 50;
        border-width: 1;
        border-radius: 5;
        padding: 3 5;
        flex: 2;
    }
    .esrbPicture{
        width: 50;
        height: 50;
    }

    .gameDescriptionContainer{
        border-top-width: 1;
        border-bottom-width: 1;
        border-color: #333;
        width: 280;
        padding: 1;
    }
    .gameDescriptionClosed{
        height: 100;
        background: linear-gradient(0deg, #333 2%, white 36%);
    }
    .gameDescriptionOpen{
        height: 100%;
        background: linear-gradient(0deg, #333 0%, white 0%);
    }
    .gameDescription{
        width: 250;
        font-size: 16;
        margin-top: 10;
        margin-left: 8;
    }
    .coverLayout{
        height: 100;
        width: 100%;
        background-color: transparent;
        top: 0;
        left: 0;
        z-index: 1
    }
    .coverLayoutText{
        top: 75;
        left: 75;
    }

    
</style>