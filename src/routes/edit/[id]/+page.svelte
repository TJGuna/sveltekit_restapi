<script>
    import { onMount } from 'svelte';
    import axios from 'axios';
    import {goto} from "$app/navigation";
    let id = '';
    let book = {
        id: '',
        title: '',
        author: ''
    };

    async function updateBook(event) {
        event.preventDefault(); // Prevent the default form submission

        try {
            const response = await axios.put(`http://localhost:8080/books/${book.id}`, book);
            goto('/');
        } catch (error) {
            console.error(error);
        }
    }

    onMount(async () => {
        try {
            const url = window.location.pathname;
            console.log(url);
            id = url.split('/')[2];
            console.log(id);
            // const id = '2'; // Replace with the actual book ID
            const response = await axios.get(`http://localhost:8080/books/${id}`);
            book = response.data;
        } catch (error) {
            console.error(error);
        }
    });
</script>

<h1 class="text-2xl font-bold mb-4">{book.Title}</h1>
<form on:submit={updateBook}>
    <input class="p-2 border-2" placeholder="Title" bind:value={book.title} required>
    <input class="p-2 border-2" placeholder="Author" bind:value={book.author} required>
    <button type="submit" class="bg-blue-500 text-white px-4 py-2">
        Update Book
    </button>
</form>
