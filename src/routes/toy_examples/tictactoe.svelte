<script type="text/javascript">
    const player_x = 'X';
    const player_o = 'O';
    let ai_enabled = false;

    const cell_coordinates = [...Array(3*3).keys()].map(i => [Math.floor(i / 3), i % 3]);
    const cell_id = (i, j) => `${i}${j}`; // cause JS doean't have tuples or equals between arrays/objs... :rolls_eyes:

	const plays = { [player_x]: [], [player_o]: [] };

    const rows = [0, 1, 2].map(i => [cell_id(i, 0), cell_id(i, 1), cell_id(i, 2)]);
    const columns = [0, 1, 2].map(j => [cell_id(0, j), cell_id(1, j), cell_id(2, j)]);
    const diagonal_1 = [cell_id(0, 0), cell_id(1, 1), cell_id(2, 2)];
    const diagonal_2 = [cell_id(0, 2), cell_id(1, 1), cell_id(2, 0)];
    const winning_conditions = [...rows, ...columns, diagonal_1, diagonal_2];

    function player_won(player_plays) {
        return winning_conditions.some(win_condition => win_condition.every(cell => player_plays.includes(cell)));        
    }

    $: current_player = plays[player_x].length <= plays[player_o].length ? player_x : player_o;
    $: all_plays = [...plays[player_x], ...plays[player_o]];
    $: x_won = player_won(plays[player_x]);
    $: o_won = player_won(plays[player_o]);
    $: game_over = x_won || o_won || all_plays.length >= 9;

    function set_place(i, j) {
        plays[current_player] = [...plays[current_player], cell_id(i, j)];
        
        if (ai_enabled && current_player === player_x && !player_won(plays[player_x])) {
            const available_coordinates = cell_coordinates.filter(([i, j]) => ![...plays[player_x], ...plays[player_o]].includes(cell_id(i, j)));
            const [i, j] = ai_move(available_coordinates);
            plays[player_o] = [...plays[player_o], cell_id(i, j)];
        }

        plays = plays;
    }

    function cell_value(plays, i, j) {
        if (plays[player_x].includes(cell_id(i, j))) return player_x;
        if (plays[player_o].includes(cell_id(i, j))) return player_o;
        return ' ';
    }

    const cell_disabled = (plays, i, j)  => game_over || plays.includes(cell_id(i, j));

    function restart() {
        plays[player_x] = [];
        plays[player_o] = [];
    }

    const ai_move = available_plays => available_plays[Math.floor(Math.random() * available_plays.length)];
</script>


<h1>Tic Tac Toe</h1>
<h3>Current player: {current_player}</h3>
<label>Enable AI: <input type="checkbox" bind:checked={ai_enabled}/></label>
<div class='board'>
    {#each cell_coordinates as [i, j] (cell_id(i, j)) }
    <button class="grid" data-player={cell_value(plays, i, j)} disabled={cell_disabled(all_plays, i, j)} on:click={() => set_place(i, j)}/>
    {/each}
</div>

{#if x_won } <h2>X won!</h2> {/if}
{#if o_won } <h2>O won!</h2> {/if}
{#if game_over } <button on:click={restart}>Restart</button> {/if}


<style type="text/css">
    .board {
        display: grid;
        grid-template: repeat(3, 10vw) / repeat(3, 10vw);
    }

    button.grid {
        background: #fff;
        border: 1px solid #999;
        
        font-size: 8vw;
        font-weight: bold;
        text-align: center;
        text-transform: uppercase;
    }

    button.grid:disabled {
        background: #f0f0f0;
    }

    button.grid[data-player='X']:after { 
        content: 'X';
        color: red;
    }
    
    button.grid[data-player='O']:after { 
        content: 'O'; 
        color: green; 
    }
</style>
