<script>
    import { closeModal } from 'svelte-native'
    import Genre from '../components/Genre.svelte'
    export let result

</script>

<frame>
    <page>
        <actionBar title='{result.name}' />
        <scrollView>
            <stackLayout style='padding:24'>
                <image src='{result.background_image}' alt="cover Image" />
                <label class="h5" text="Released: {result.released}" />
                <label class="h3" text='Rating: {result.rating} of 5' />
            </stackLayout>
        </scrollView>
    </page>
</frame>