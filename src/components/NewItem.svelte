<script>
    import {createEventDispatcher} from 'svelte';

    export let status = null;
    let information = {status, title: '', description: '', assign: '', estimate: ''}
    const dispatch = createEventDispatcher();

    function handleSubmit() {
        dispatch('submit', {
            ...information
        });

        information = {...information, title: '', description: '', assign: '', estimate: ''};
    }

</script>

<div class="form">
    <input type="text" placeholder="Title" bind:value={information.title} name="title"/>
    <input type="textarea" placeholder="Description" bind:value={information.description} name="description"/>
    <input type="text" placeholder="Assign to" bind:value={information.assign} name="assign"/>
    <input type="number" placeholder="Story point" bind:value={information.estimate} name="Estimate" min="0" max="40"/>
    <button on:click={handleSubmit}>submit</button>
</div>

<style>
    .form {
        position: relative;
        padding: 1.5em 2.5em 1.5em 2em;
        margin: 0 0 1.5em 0;
        border-radius: 10px;
        user-select: none;
        background-color: #FFFFFF;
        box-shadow: 0px 0px 8px -1px #dbdbdb;
        color: #adadad;
    }

    input, button {
        border-radius: 8px;
        width: 100%;
        outline: none;
    }
</style>