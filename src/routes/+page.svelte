<script>
    import { onMount } from "svelte";
    import { Input } from "$lib/components/ui/input/index.js";
    import Glow from "$lib/Glow.svelte";

    let selectedBook = {
        name: "dc-testament",
        book: "dc",
    };
    let selectedVerse = "84.45";

    let books = {
        ot: {
            name: "Old Testament",
            books: [
                "gen",
                "ex",
                "lev",
                "num",
                "deut",
                "josh",
                "judg",
                "ruth",
                "1-sam",
                "2-sam",
                "1-kgs",
                "2-kgs",
                "1-chr",
                "2-chr",
                "ezra",
                "neh",
                "esth",
                "job",
                "ps",
                "prov",
                "eccl",
                "song",
                "isa",
                "jer",
                "lam",
                "ezek",
                "dan",
                "hosea",
                "joel",
                "amos",
                "obad",
                "jonah",
                "micah",
                "nahum",
                "hab",
                "zeph",
                "hag",
                "zech",
                "mal",
            ],
        },
        nt: {
            name: "New Testament",
            books: [
                "matt",
                "mark",
                "luke",
                "john",
                "acts",
                "rom",
                "1-cor",
                "2-cor",
                "gal",
                "eph",
                "philip",
                "col",
                "1-thes",
                "2-thes",
                "1-tim",
                "2-tim",
                "titus",
                "philem",
                "heb",
                "james",
                "1-pet",
                "2-pet",
                "1-jn",
                "2-jn",
                "3-jn",
                "jude",
                "rev",
            ],
        },
        pgp: {
            name: "Pearl of Great Price",
            books: ["moses", "abr", "js-m", "js-h", "a-of-f"],
        },
        bofm: {
            name: "Book of Mormon",
            books: [
                "1-ne",
                "2-ne",
                "jacob",
                "enos",
                "jarom",
                "omni",
                "w-of-m",
                "mosiah",
                "alma",
                "hel",
                "3-ne",
                "4-ne",
                "morm",
                "ether",
                "moro",
            ],
        },
        "dc-testament": {
            name: "Doctrine and Covenants",
            books: ["dc"],
        },
    };

    let scriptures = [
        "/eng/scriptures/pgp/moses/1.p39",
        "/eng/scriptures/pgp/moses/7.p18",
        "/eng/scriptures/pgp/abr/3.22-23",
        "/eng/scriptures/ot/gen/1.26-27",
        "/eng/scriptures/ot/gen/2.24",
        "/eng/scriptures/ot/gen/39.9",
        "eng/scriptures/ot/ex/19.5-6",
        "eng/scriptures/ot/ex/20.3-17",
    ];
    let iScripture = 0;

    const getScripture = async (url) => {
        let data = await fetch(
            "https://www.churchofjesuschrist.org/content/api/v3",
            {
                method: "POST",
                headers: {
                    "Content-Type": "application/json",
                },
                body: JSON.stringify({ uris: [url] }),
            },
        ).then((response) => response.json());

        console.log(data);

        return data;
    };

    onMount(async () => {
        currentScripture = await getScripture(
            "/eng/scriptures/" +
                selectedBook.name +
                "/" +
                selectedBook.book +
                "/" +
                selectedVerse,
        );
    });

    const nextScripture = async () => {
        // iScripture++;
        // if (iScripture >= scriptures.length) {
        //     iScripture = 0;
        // }
        currentScripture = await getScripture(
            "/eng/scriptures/" +
                selectedBook.name +
                "/" +
                selectedBook.book +
                "/" +
                selectedVerse,
        );
    };

    let currentScripture = {};
</script>

<div
    class="h-screen w-screen flex flex-col gap-16 items-center justify-center bg-black text-white p-20"
>
    <!-- <div class="flex flex-row gap-4">
        <p class="white-glow">Names of God</p>
        <p class="green-glow">Actions</p>
        <p class="orange-glow">Orange</p>
        <p class="purple-glow">Purple</p>
        <p class="teal-glow">Characteristics and Attributes</p>
        <p class="gold-glow">Exaltation</p>
        <p class="red-glow">Red</p>
        <p class="pink-glow">The How</p>
        <p class="silver-glow">Silver</p>
        <p class="brown-glow">Brown</p>
        <p class="coral-glow">important</p>
    </div> -->
    <div class="flex flex-col gap-4 h-full overflow-auto">
        {#key currentScripture}
            {#if currentScripture[Object.keys(currentScripture)[0]]}
                {#each currentScripture[Object.keys(currentScripture)[0]].content as verse}
                    <Glow text={verse.markup} />
                {/each}
                <b
                    >{currentScripture[Object.keys(currentScripture)[0]]
                        .referenceURIDisplayText}
                </b>
            {/if}
        {/key}
    </div>
    <div class="flex flex-row gap-3">
        <select class="bg-black text-white" bind:value={selectedBook}>
            {#each Object.entries(books) as [key, value]}
                <optgroup label={value.name}>
                    {#each value.books as book}
                        <option value={{ name: key, book: book }}>{book}</option
                        >
                    {/each}
                </optgroup>
            {/each}
        </select>
        <Input
            type="text"
            name="verses"
            placeholder="1.2"
            bind:value={selectedVerse}
        />
    </div>
    <button
        on:click={nextScripture}
        class="py-2 px-4 bg-blue-500 hover:bg-blue-600 rounded"
        >Load Scripture</button
    >
</div>
