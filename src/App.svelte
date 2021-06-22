<script>
    import {quintOut} from 'svelte/easing';
    import {crossfade, slide} from 'svelte/transition';
    import NewItem from "./components/NewItem.svelte";

    let showForm = {todo: false, inProgress: false, done: false};

    const [send, receive] = crossfade({
        duration: d => Math.sqrt(d * 200),
        fallback(node, params) {
            const style = getComputedStyle(node);
            const transform = style.transform === 'node' ? '' : style.transform;

            return {
                duration: 600,
                easing: quintOut,
                css: t => `
                    transform: ${transform} scale(${t});
                    opacity: ${t}
                `
            };
        }
    });

    let uid = 1;

    let items = [
        {id: uid++, status: 'todo', title: 'Install project', description: 'Install packages', assign: 'Jim', estimate: 1},
        {
            id: uid++,
            status: 'todo',
            title: 'Form component',
            description: 'Create form component  ',
            assign: 'Jim',
            estimate: 8
        },
        {id: uid++, status: 'done', title: 'Items page', description: 'Create items page and add to main', assign: 'Jim', estimate: 8},
        {id: uid++, status: 'todo', title: 'Deploy project', description: 'Deploy project to server', assign: 'Jim', estimate: 3},
        {
            id: uid++,
            status: 'inProgress',
            title: 'Design pages',
            description: 'Design all components',
            assign: 'Sara',
            estimate: 5
        },
        {id: uid++, status: 'todo', title: 'Flow test', description: 'Test all flow test', assign: 'Jim', estimate: 13},
    ];


    function add(event) {
        const todo = {
            id: uid++,
            title: event.detail.title,
            description: event.detail.description,
            assign: event.detail.assign,
            estimate: event.detail.estimate,
            status: event.detail.status
        }

        items = [todo, ...items];

        showForm[event.detail.status] = false;
    }

    function remove(item) {
        items = items.filter(t => t !== item);
    }

    function mark(item, status) {
        item.status = status;
        remove(item);
        items = items.concat(item);
    }
</script>

<div class="board">
    <div class="left item-box">
        <h2>To do</h2>
        <label class="add-item" role="button">
            <input type="checkbox" bind:checked={showForm.todo}/>
            +
        </label>

        {#if showForm.todo}
            <div transition:slide>
                <NewItem status='todo' on:submit={add}/>
            </div>
        {/if}
        {#each items.filter(t => t.status === 'todo') as item(item.id)}
            <label
                    in:receive="{{key: item.id}}"
                    out:send="{{key: item.id}}"
            >
                <input type="checkbox" on:change={()=>mark(item, 'inProgress')}>
                <span class="title">{item.title}</span>
                <p class="description">{item.description}</p>
                <div class="footer">
                    <div class="assign">{item.assign}</div>
                    <div class="estimate">{item.estimate}</div>
                </div>

                <button mode="delete" on:click="{() => remove(item)}">remove</button>
            </label>
        {/each}
    </div>

    <div class="center item-box">
        <h2>In progress</h2>
        <label class="add-item" role="button">
            <input type="checkbox" bind:checked={showForm.inProgress}/>
            +
        </label>

        {#if showForm.inProgress}
            <div transition:slide>
                <NewItem status='inProgress' on:submit={add}/>
            </div>
        {/if}
        {#each items.filter(t => t.status === 'inProgress') as item(item.id)}
            <label
                    in:receive="{{key: item.id}}"
                    out:send="{{key: item.id}}"
            >
                <input type="checkbox" on:change={()=>mark(item, 'done')}>
                <span class="title">{item.title}</span>
                <p class="description">{item.description}</p>
                <div class="footer">
                    <div class="assign">{item.assign}</div>
                    <div class="estimate">{item.estimate}</div>
                </div>
                <button mode="delete" on:click="{() => remove(item)}">remove</button>
            </label>
        {/each}
    </div>

    <div class="right item-box">
        <h2>Done</h2>
        <label class="add-item" role="button">
            <input type="checkbox" bind:checked={showForm.done}/>
            +
        </label>

        {#if showForm.done}
            <div transition:slide>
                <NewItem status='done' on:submit={add}/>
            </div>
        {/if}
        {#each items.filter(t => t.status === 'done') as item(item.id)}
            <label
                    class="done"
                    in:receive="{{key: item.id}}"
                    out:send="{{key: item.id}}"
            >
                <input type="checkbox" on:change={()=>{mark(item, 'todo')}}>
                <span class="title">{item.title}</span>
                <p class="description">{item.description}</p>
                <div class="footer">
                    <div class="assign">{item.assign}</div>
                    <div class="estimate">{item.estimate}</div>
                </div>
                <button mode="delete" on:click="{() => remove(item)}">Remove</button>
            </label>
        {/each}
    </div>
</div>

<style>
    .board {
        display: grid;
        grid-template-columns: 1fr 1fr 1fr;
        grid-gap: 2em;
        max-width: 70em;
        margin: 0 auto;
    }

    @media (max-width: 800px) {
        .board {
            grid-template-columns: 1fr;
        }
    }
    .board > input {
        font-size: 1.4em;
        grid-column: 1/3;
    }

    h2 {
        font-size: 1.75em;
        font-weight: 500;
        user-select: none;
        margin: 0 0 0.5em 0;
    }

    label {
        position: relative;
        padding: 0.5em 2.5em 0.5em 2em;
        margin: 0 0 1.5em 0;
        border-radius: 10px;
        user-select: none;
        background-color: #FFFFFF;
        box-shadow: 0px 0px 8px -1px #DBDBDB;
        color: #adadad;
    }

    .add-item {
        color: #bccac1;
        background: #e0e8e7;
        box-shadow: 0px 0px 8px -1px #e0e8e7;
        border-radius: 8px;
        padding: 8px 15px;
        text-align: center;
        font-weight: bold;
        font-size: 1.25em;
        margin: 0 0 1em 0;
        cursor: pointer;
    }

    .add-item input[type='checkbox'] {
        visibility: hidden;
    }

    .estimate {
        background: #6aa7b3;
        color: white;
        padding: 2px 16px;
        border-radius: 10px;
    }

    .title {
        font-weight: 600;
        color: #2f3433;
        line-height: 1.2;
    }

    .footer {
        display: flex;
        justify-content: space-between;
    }

    input[type="checkbox"] {
        visibility: hidden;
        position: absolute;
        left: 0.5em;
        top: 0.6em;
        margin: 0;
    }

    .done {
        border: 1px solid hsl(240, 8%, 90%);
        background-color: hsl(240, 8%, 98%);
    }

    button {
        position: absolute;
        top: 0;
        right: 0.2em;
        width: 2em;
        height: 100%;
        border: none;
        opacity: 0;
        transition: opacity 0.2s;
        text-indent: -9999px;
        cursor: pointer;
    }

    button[mode='delete'] {
        background: no-repeat 50% 50% url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24'%3E%3Cpath fill='%23676778' d='M12,2C17.53,2 22,6.47 22,12C22,17.53 17.53,22 12,22C6.47,22 2,17.53 2,12C2,6.47 6.47,2 12,2M17,7H14.5L13.5,6H10.5L9.5,7H7V9H17V7M9,18H15A1,1 0 0,0 16,17V10H8V17A1,1 0 0,0 9,18Z'%3E%3C/path%3E%3C/svg%3E");
        background-size: 1.4em 1.4em;
    }

    .assign {
        width: 1.5ch;
        overflow: hidden;
        white-space: nowrap;
        border-radius: 50%;
        background: darkorange;
        height: 1.5ch;
        color: white;
        font-size: 1.5em;
    }

    label:hover button {
        opacity: 1;
    }

    .item-box {
        padding: 20px 12px;
        background: #f3f6f6;
        border-top-left-radius: 20px;
        border-top-right-radius: 20px;
        height: 100vh;
    }

    :global(body) {
        background: #fefefe;
    }
</style>