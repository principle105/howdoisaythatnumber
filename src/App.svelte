<script lang="ts">
    import triplets from "./triplets";

    let numberInput: number = 0;
    let numberInputRaw: string = "";

    $: spokenNumberOutput = numberToWords(numberInput);

    const numberToWords = (num: number): string => {
        const units: string[] = [
            "",
            "one",
            "two",
            "three",
            "four",
            "five",
            "six",
            "seven",
            "eight",
            "nine",
        ];
        const tens: string[] = [
            "",
            "ten",
            "twenty",
            "thirty",
            "forty",
            "fifty",
            "sixty",
            "seventy",
            "eighty",
            "ninety",
        ];
        const teens: string[] = [
            "ten",
            "eleven",
            "twelve",
            "thirteen",
            "fourteen",
            "fifteen",
            "sixteen",
            "seventeen",
            "eighteen",
            "nineteen",
        ];

        if (num === 0) {
            return "zero";
        }

        const parts: string[] = [];

        // Checking if the number is too large
        if (num >= Math.pow(1000, triplets.length)) {
            return "Woah! that number is too large!";
        }

        // Handle triplets
        for (let i = triplets.length; i > 0; i--) {
            const scale = Math.pow(1000, i);

            if (num >= scale) {
                const current = Math.floor(num / scale);
                num %= scale;

                let words = `${numberToWords(current)} ${triplets[i]}`;

                parts.push(words);
            }
        }

        // Handle hundreds place
        if (num >= 100) {
            const hundreds = Math.floor(num / 100);
            num %= 100;

            parts.push(units[hundreds] + " hundred");
        }

        // Handle tens and units place
        if (num > 0) {
            if (num < 10) {
                parts.push(units[num]);
            } else if (num < 20) {
                parts.push(teens[num - 10]);
            } else {
                const ones = num % 10;
                const tensDigit = Math.floor(num / 10);

                parts.push(
                    tens[tensDigit] + (ones > 0 ? "-" + units[ones] : "")
                );
            }
        }

        const result = parts.join(", ");

        if (parts.length > 1 && num !== 0) {
            return result.replace(/(.*),(\s\S+)$/, "$1 and$2");
        }

        return result;
    };

    const handleNumberInput = (event) => {
        let enteredDistance = event.target.value || "0";

        if (enteredDistance.match(/^\d+$/)) {
            numberInput = parseInt(enteredDistance);
            return;
        }

        numberInputRaw = numberInput.toString();
    };
</script>

<main class="m-auto w-5/6 text-center">
    <h1 class="text-5xl mb-8">How do I say that number?</h1>
    <div>
        <input
            type="text"
            class="rounded-md outline-none shadow-md text-5xl p-3 text-center"
            bind:value={numberInputRaw}
            on:input={handleNumberInput}
        />
        <div class="mt-4">{spokenNumberOutput}</div>
    </div>
</main>

<style global>
    @tailwind utilities;
    @tailwind components;
    @tailwind base;
</style>
