<script lang="ts">
    import triplets from "./triplets";

    let numberInput: bigint = BigInt(0);
    let numberInputRaw: string = "";

    $: spokenNumberOutput = numberToWords(numberInput);

    const numberToWords = (bigNum: bigint): string => {
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

        if (bigNum === BigInt(0)) {
            return "zero";
        }

        const parts: string[] = [];

        const limit = BigInt(1000) ** BigInt(triplets.length);

        // Checking if the number is too large
        if (bigNum >= limit) {
            return "Woah! that number is too large!";
        }

        const largestScale = Math.floor(bigNum.toString().length / 3);

        // Handle triplets
        for (let i = largestScale; i > 0; i--) {
            const scale = BigInt(1000) ** BigInt(i);

            if (bigNum >= scale) {
                const current = bigNum / scale;
                bigNum %= scale;

                let words = `${numberToWords(current)} ${triplets[i]}`;

                parts.push(words);
            }
        }

        let num = Number(bigNum);

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
            numberInput = BigInt(enteredDistance);
            return;
        }

        numberInputRaw = numberInput.toString();
    };
</script>

<main class="m-auto w-5/6 text-center">
    <div class="mb-10">
        <h1 class="text-3xl md:text-4xl lg:text-5xl md:mb-2.5 mb-1">
            How do I say that number?
        </h1>
        <h1 class="text-zinc-600">Type any number in the box below.</h1>
    </div>
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
