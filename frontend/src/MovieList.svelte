<script lang="ts">
    import type {Movie} from "./types/Movie";
    import {goto} from "$app/navigation";
    import MovieUpdate from "./MovieUpdate.svelte";

    export let movies: Movie[];

    let showMovieUpdate = {}; // 각 movie.id를 키로 가지는 객체를 생성

    function toggleMovieUpdate(movieId) {
        showMovieUpdate[movieId] = !showMovieUpdate[movieId];
        showMovieUpdate = {...showMovieUpdate}; // Svelte가 변화를 감지하도록 객체를 새로 할당
    }

    async function deleteMovie(movieId) {
        try {
            const response = await fetch(`http://localhost:8080/api/v1/movies/${movieId}`, {
                method: 'DELETE'
            });

            if (response.ok) {
                movies = movies.filter(movie => movie.id !== movieId);
            } else {
                console.error('영화 삭제 실패');
            }
        } catch (error) {
            console.error('영화 삭제 실패:', error);
        }
    }

</script>

<style>
    .modal {
        display: none; /* Hidden by default */
        position: fixed; /* Stay in place */
        z-index: 1; /* Sit on top */
        left: 0;
        top: 0;
        width: 100%; /* Full width */
        height: 100%; /* Full height */
        overflow: auto; /* Enable scroll if needed */
        background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
    }

    /* Modal Content/Box */
    .modal-content {
        background-color: #fefefe;
        margin: 15% auto; /* 15% from the top and centered */
        padding: 20px;
        border: 1px solid #888;
        width: 40%; /* Could be more or less, depending on screen size */
    }

    .movie-container {
        display: grid;
        grid-template-columns: repeat(4, 1fr);
        gap: 16px;
    }
    .movie-item {
        padding: 16px;
        border: 1px solid #ccc;
        text-align: center;
    }
    .movie-actions {
        display: flex;
        justify-content: space-between;
        margin-top: 8px;
    }

     .movie-container {
         display: grid;
         grid-template-columns: repeat(3, 1fr);
         gap: 16px;
     }

    .movie-actions button {
        padding: 10px 20px;
        margin: 5px;
        border: none;
        border-radius: 5px;
        cursor: pointer;
        transition: background-color 0.3s;
    }
    .movie-actions button:hover {
        background-color: #f2f2f2;
    }
</style>

<div class="movie-container">
    {#each movies as movie}
        <div class="movie-wrap">
            <div class="movie-item" role="button" tabindex="0"
                 on:click={() => goto(`/movie/${movie.id}`)}
                 on:keydown={(e) => { if (e.key === 'Enter') goto(`/movie/${movie.id}`)}}>
                <h3>{movie.title}</h3>
            </div>
            <div class="movie-actions">
                <button on:click={() => toggleMovieUpdate(movie.id)}>수정</button>

                {#if showMovieUpdate[movie.id]}
                    <div class="modal" style="display: block;">
                        <div class="modal-content">
                            <span class="close-button" on:click={() => toggleMovieUpdate(movie.id)}>&times;</span>
                            <MovieUpdate movie={movie}/>
                        </div>
                    </div>
                {/if}
                <button on:click={() => deleteMovie(movie.id)}>삭제</button>
            </div>
        </div>
    {/each}
</div>
