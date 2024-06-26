<script>
    import { Input, Label, Spinner } from "flowbite-svelte";
    import { appStatusInfo, setAppStatusError } from "../store.ts";
    const { url, pages, id } = $appStatusInfo;

    let answer = "";
    let loading = false;

    const numOfImagesToShow = Math.min(pages, 2);
    const images = Array.from({ length: numOfImagesToShow }, (_, i) => {
        const page = i + 1;
        return url
            .replace("/upload/", `/upload/w_400,h_540,c_fill,pg_${page}/`)
            .replace(".pdf", ".jpg");
    });

    const handleSubmit = async (event) => {
        event.preventDefault();
        loading = true;
        const question = event.target.question.value;

        const searchParams = new URLSearchParams();
        searchParams.append("id", id);
        searchParams.append("question", question);

        try {
            const res = await fetch(`/api/ask?${searchParams.toString()}`, {
                headers: {
                    "Content-Type": "application/json",
                },
            });

            if (!res.ok) {
                loading = false;
                console.error("Failed to ask question");
                return;
            }

            const { response } = await res.json();
            answer = response;
        } catch (e) {
            setAppStatusError();
        } finally {
            loading = false;
        }
    };
</script>

<div class="grid grid-cols-2">
    <div>
        <div class="grid grid-cols-2 gap-2">
            {#each images as image}
                <img
                    src={image}
                    alt="pdf page"
                    class="w-full h-auto rounded aspect-[400/540]"
                />
            {/each}
        </div>

        <form on:submit={handleSubmit} class="mt-8">
            <Label for="question" class="block mb-2 text-white"
                >Ask something about your file</Label
            >
            <Input id="question" required placeholder="Summarize this file" />
        </form>

        {#if loading}
            <div
                class="mt-10 flex justify-center items-center flex-col gap-y-2"
            >
                <Spinner color="blue" />
                <p class="text-white">Waiting for an answer...</p>
            </div>
        {/if}
    </div>

    {#if answer}
        <div class="mx-5 mt-8 mb-10 flex justify-center items-center flex-col text-white">
            <p class="font-bold size-4">Answer:</p>
            <p>{answer}</p>
        </div>
    {/if}
</div>
