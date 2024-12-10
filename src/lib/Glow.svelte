<script>
    import { onMount } from "svelte";

    export let text;

    let textElement;

    const removeAnchorWithSup = (htmlString) => {
        // Create a temporary DOM element to parse the HTML string
        const parser = new DOMParser();
        const doc = parser.parseFromString(htmlString, "text/html");

        // Select all <a> elements
        const anchorElements = doc.querySelectorAll("a");

        anchorElements.forEach((anchor) => {
            // Check if the <a> tag contains a <sup>
            if (anchor.querySelector("sup")) {
                // Replace the <a> element with its inner text content (excluding the <sup>)
                const anchorText = anchor.textContent; // Gets all text inside the <a>, including from <sup>
                const parentElement = anchor.parentNode;
                if (parentElement) {
                    const textNode = document.createTextNode(anchorText);
                    parentElement.replaceChild(textNode, anchor);
                }
            }
        });

        const pageBreakElements = doc.querySelectorAll(".page-break");

        pageBreakElements.forEach((pageBreak) => {
            const parentElement = pageBreak.parentNode;
            if (parentElement) {
                parentElement.removeChild(pageBreak);
            }
        });

        // Serialize the modified HTML back to a string
        return doc.body.innerHTML;
    };

    // Function to wrap matched words in a <span> with the corresponding class
    const wrapText = (word, category) => {
        return `<span class="${category}-glow">${word}</span>`;
    };

    const escapeRegExp = (str) => {
        return str.replace(/[.*+?^=!:${}()|\[\]\/\\]/g, "\\$&");
    };

    // On Mount, everytime we see the word "Jesus" in the scripture, we will add a glow to it.

    onMount(() => {
        // Define categories with arrays of words/phrases
        const categories = {
            white: [
                "God",
                "Lord",
                "Eternal Father",
                "Jesus Christ",
                "Jesus",
                "King of the Jews",
                "Savior",
                "Redeemer",
                "Christ",
                "the Father",
                "Son",
                "Holy One",
                "Lamb of God",
                "The Almighty",
                "Jehovah",
                "Prince of Peace",
                "The Messiah",
                "The King of Kings",
                "The Light",
                "The Word",
                "The Good Shepherd",
                "Immanuel",
                "Emmanuel",
                "The Great I Am",
                "Spirit",
                "great Creator",
                "his word",
                "Spirit of the Lord",
                "light",
                "Messiah",
                "Lamb",
                "Holy Ghost",
                "Almighty",
                "endless",
                "Only Begotten",
                "the greatest of all",
                "everlasting to everlasting",
                "Comforter",
                "the Firstborn",
                "King",
                "Creator",
                "bread",
                "waters of life",
                "my Father",
                "in his name",
                "mediator",
            ],
            teal: [
                "Knowledge",
                "Righteous",
                "Righteousness",
                "Love",
                "Mercy",
                "Truth",
                "valiant",
                "Power",
                "Judgment",
                "Goodness",
                "Good",
                "meek",
                "lowly in heart",
                "lowliness in heart",
                "lowly of heart",
                "humble",
                "wisdom",
                "merciful",
                "perfect",
                "perfection",
                "happy",
                "joy",
                "grace",
                "kind",
                "charity",
                "highest",
                "virtue",
                "hope",
                "pure",
                "believing",
                "beloved",
                "godliness",
                "true",
                "meekness",
                "suffereth long",
                "firm mind",
                "greatness",
                "long-suffering",
                "just",
                "soberness",
                "sober",
                "peaceable",
                "authority",
                "everlasting",
                "redeeming",
                "holy",
                "living",
                "faithful",
                "clean",
                "honorable",
                "spotless",
                "steadfast",
                "solemnity",
                "heavenly",
                "high",
                "holiest",
                "holiness",
                "wisely",
                "maker",
                "builder",
                "friends",
                "friend",
                "wise",
                "worthy",
                "peace",
                "marvelous",
                "gracious",
                "equal",
                "might",
                "dominion",
                "honor",
                "excellent",
            ],
            gold: [
                "mysteries",
                "blessed",
                "eternity",
                "blessings",
                "favored",
                "learning",
                "celestial",
                "enlighten",
                "chosen",
                "peculiar",
                "treasure",
                "kingdom",
                "dwelt upon a rock",
                "lifted up",
                "rest",
                "mountain",
                "eternal life",
                "glory",
                "saved",
                "heaven",
                "tree of life",
                "eternal round",
                "saving",
                "called",
                "transfigured",
                "transfiguration",
                "miracles",
                "ascended",
                "covenants",
                "atonement",
                "eternally",
                "delivered",
                "promises",
                "resurrection",
                "life eternal",
                "promise",
                "salvation",
                "redemption",
                "redeem",
                "purified",
                "cleansed",
                "covenant",
                "enlighteneth",
                "heavens",
                "oath",
                "Priesthood",
                "priesthoods",
                "consecrated",

                "overcome",
                "appointed",
                "ordinances",
                "temple",
                "order",
                "eternal",
                "enlightened",
                "ordained",
                "redeemed",
                "washed",
                "raise",
                "sealed",

                "sanctified",
            ],
            green: [
                "learn",
                "repent",
                "manifest",
                "worshipped",
                "administereth",
                "pray",
                "live",
                "rejoiceth",
                "beareth",
                "deliver",
                "spake",
                "act",
                "keeping",
                "understand",
                "prayed",
                "worship",
                "delight",
                "repentance",
                "repenteth",
                "known",
                "increase",
                "endureth",
                "reveal",
                "strengthened",
                "baptized",
                "believe",
                "knoweth",
                "knowing",
                "obeyed",
                "dwelling",
                "looked",
                "judged",
                "testifying",
                "speaketh",
                "prepared",
                "believeth",
                "works",
                "dwell",
                "seen",
                "understood",
                "purify",
                "seeing",
                "bear",
                "judge",
                "remember",
                "confessed",
                "embraced",
                "faith",
                "hopeth",
                "baptize",
                "ministration",
                "seeketh",
                "seek",
                "see",
                "obtain",
                "hear",
                "endure",
                "know",
                "receive",
                "received",
                "obey",
                "minister",
                "ministering",
                "cleave",
                "bear testimony",
                "work",
                "prepare",
                "prepareth",
                "come unto",
                "confesses",
                "confess",
                "forgive",
                "seeking",
                "ask",
                "offereth",
                "prayeth",
                "search",
                "lay hold",
                "dwelleth",
                "knock",
                "obtained",
                "forsake",
                "bear record",
                "retain in remembrance",
                "retain a remission of your sins",
                "grow",
                "created",
                "rejoice",
                "establish",
                "look forward",
                "serve",
                "exercise",
                "administer",
                "remembrance",
                "speak",
                "humbled",
                "change",
                "administering",
                "revealed",
                "trust",
                "testify",
                "preach",
                "hearken",
                "hearkeneth",
                "prayer",
                "bearing testimony",
                "magnifying",
                "renewing",
                "offered",
                "obtaining",
                "sacrifice",
                "united",
                "restoration",
                "spoken",
                "stand",
                "gathering",
                "forgiven",
                "keep",
                "become",
                "hoped",
                "saw",
                "heard",
                "believed",
                "made",
            ],
            coral: [
                "diligently",
                "diligence",
                "sincerity",
                "exceedingly",
                "great",
                "daily",
                "continually",
                "steadfastly",
                "always",
                "shall",
                "that",
                "wherefore",
                "behold",
                "if",
                "but",
                "therefore",
                "forever",
                "beware",
                "immediately",
                "fulness",
                "all",
            ],
            pink: [
                "commandment",
                "commanded",
                "prophets",
                "ministry",
                "vessels",
                "revelation",
                "hearts",
                "spiritual",
                "church",
                "commandments",
                "laws",
                "priest",
                "law",
                "revelations",
                "wonders",
                "heart",
                "gospel",
                "angel",
                "angels",
                "immortality",
                "vision",
                "testimony",
                "priests",
                "bishop",
                "elder",
                "elders",
            ],
            purple: [
                "Israel",
                "children of men",
                "sons of God",
                "residue of men",
                "sheep",
                "Zion",
                "vineyard",
            ],
            red: ["blood"],
            // Add more as needed
        };

        let cleanText = removeAnchorWithSup(text);

        console.log(cleanText);

        // Loop through each category and match the words in the text
        for (const [category, words] of Object.entries(categories)) {
            words.forEach((word) => {
                const escapedWord = escapeRegExp(word); // Escape special characters in the word
                const regex = new RegExp(
                    `(?:^|\\W)${escapedWord}(?=\\W|$)`,
                    "gi",
                ); // Match the word/phrase globally (case-insensitive)

                // Replace matching word with the wrapped version
                console.log(word);
                cleanText = cleanText.replace(regex, (match) =>
                    wrapText(match, category),
                );
                console.log(cleanText);
            });
        }

        text = cleanText;
    });
</script>

<p class="text-2xl">{@html text}</p>
