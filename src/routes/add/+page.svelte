<script>
    import { onMount } from 'svelte';
    import axios from 'axios';
    import {goto} from "$app/navigation";

    let books = [];
    let book = {
        ID:0,
        Title: '',
        Author: '',
    };
    onMount(async () => {
        try {
            const response = await axios.get('http://localhost:8080/books/');
            books = response.data;
        } catch (error) {
            console.error(error);
        }
    });

    async function addBook() {
        let id= books.length + 1;
        book.ID = id.toString();
        try {
            const response = await axios.post('http://localhost:8080/books/', book);
            goto('/');
        } catch (error) {
            console.error(error);
        }
    }

</script>

    <h1 class="text-2xl font-bold mb-4">Books</h1>

<form on:submit|preventDefault={addBook}>
    <input class="p-2 border-2" placeholder="Title" bind:value={book.Title}  required>
    <input class="p-2 border-2" placeholder="Author" bind:value={book.Author}  required>
    <button type="submit" class="bg-blue-500 text-white px-4 py-2">
        Upload Book
    </button>
</form>
