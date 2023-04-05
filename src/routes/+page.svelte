<script>
    import Navbar from "$lib/Navbar.svelte";
</script>

<html>
    <head>
        <title>My App</title>
    </head>
    <body>
        <Navbar items={[{text: "Test", url: "https://google.com"}]} selected={0}/>
    </body>
</html>