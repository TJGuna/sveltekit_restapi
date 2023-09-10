<script >
    import { onMount } from 'svelte';
    import axios from 'axios';
    import {goto} from "$app/navigation";
    import edit from '$lib/assets/svg/edit.svg';
    import del from '$lib/assets/svg/delete.svg';

    let books = [];
    let masterToggle = false;
    let masterIndeterminate = false;
    let rows = books.map((item) => ({
        ...item,
        checked: false,
    }));
    onMount(async () => {
        try {
            const response = await axios.get('http://localhost:8080/books/');
            books = response.data;
        } catch (error) {
            console.error(error);
        }
    });
    function toggleAll() {
        rows = rows.map((item) => ({
            ...item,
            checked: masterToggle,
        }));
        masterIndeterminate = false;
    }

    function toggleRow(item) {
        masterToggle = rows.every((item) => item.checked);
        masterIndeterminate = !masterToggle && rows.some((item) => item.checked);
    }

    $: selectedRows = rows.filter((item) => item.checked);
    async function deleteBook(id) {
        try {
            await axios.delete('http://localhost:8080/books/'+id);
            books = books.filter((item) => item.id !== id);
        } catch (error) {
            console.error(error);
        }
    }
</script>

<main class=" space-y-8">

    <h1 class=" flex justify-between items-center  border-2 rounded shadow-md shadow-cyan-500 p-4">
        <p class="text-2xl font-bold">Books</p>
        <button class="bg-blue-500 text-white px-4 py-2 " on:click={()=>goto('/add')}>
            Add Book
        </button>
    </h1>




    {#if books.length === 0}
        <p>No books found.</p>
    {:else}

        <table class="w-full borde2 p-2">
            <thead class="border bg-gray-400 h-10 p-2">
                <tr class="">
                    <th class="py-2 px-3 text-left">
                        <input
                                type="checkbox"
                                class="h-4 w-4 rounded  text-primary  focus:ring-0 border-primary-extra-dark"
                                bind:checked={masterToggle}
                                bind:indeterminate={masterIndeterminate}
                                on:change={toggleAll}
                        />
                    </th>
                    <th>Id</th>
                    <th>Title</th>
                    <th>Author</th>
                    <th class="text-end px-4">Actions</th>
                </tr>
            </thead>
            <tbody class="border  h-8">
            {#each books as book}
                <tr class=" odd:bg-gray-100 even:bg-gray-50 ">
                    <td class="py-2 px-3 ">
                        <input
                                type="checkbox"
                                class=" h-4 w-4 rounded text-primary  focus:ring-0 border-primary-extra-dark"
                                bind:checked={book.checked}
                                on:change={() => toggleRow(book)}
                        />
                    </td>
                    <td>{book.id}</td>
                    <td>{book.title}</td>
                    <td>{book.author}</td>
                    <td class="text-end px-4  flex justify-end items-center">
                        <button class=" px-4 py-2" on:click={()=>goto('/edit/'+book.id)}>
                            <img class="w-4" src={edit}>
                        </button>
                        <button class=" px-4 py-2 " on:click={()=>deleteBook(book.id)}>
                            <img class="w-4" src={del}>
                        </button>
                    </td>
                </tr>
            {/each}
        </table>
    {/if}

</main>
