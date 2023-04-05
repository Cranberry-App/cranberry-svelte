<script>
    import Navbar from "$lib/Navbar.svelte";

    let imageUrl = "https://cdn.pixabay.com/photo/2015/04/23/22/00/tree-736885__480.jpg";
</script>

<html lang="en">
    <head>
        <title>Cranberry</title>
    </head>
    <body>
        <Navbar
            items={[
                {text: "Test", url: "https://google.com"},
                {text: "Test2", url: "https://jimii.xyz"}
            ]}
            right={{
                name: "menu",
                image: imageUrl,
                items: [
                    {text: "Test", url: "https://google.com"},
                    {text: "Test2", url: "https://jimii.xyz"}
                ]
            }}
        />
    </body>
</html>

<style lang="scss">
    body {
        margin: 0;
        padding: 0;
    }
</style>