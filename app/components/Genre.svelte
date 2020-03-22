<script>
    let samma = []

    const test = () => {
        result.genres.map((genre, key) => {
                console.log(genre.name)
                samma = genre.name
            }
        )
    }
    test()
</script>

<page>
    <label text="{samma}"/>
</page>