<script type="text/javascript">
    const player_x = 'X';
    const player_o = 'O';

	const plays = {
        [player_x]: [],
        [player_o]: [],
    }

    $: current_player = plays[player_x].length <= plays[player_o].length ? player_x : player_o;
    $: all_plays = [...plays[player_x], ...plays[player_o]]

    const cell_id = (i, j) => `${i}${j}`; // cause JS doean't have tuples or equals between arrays/objs... :rolls_eyes:

    const rows = [0, 1, 2].map(i => [cell_id(i, 0), cell_id(i, 1), cell_id(i, 2)]);
    const columns = [0, 1, 2].map(j => [cell_id(0, j), cell_id(1, j), cell_id(2, j)]);
    const diagonal_1 = [cell_id(0, 0), cell_id(1, 1), cell_id(2, 2)];
    const diagonal_2 = [cell_id(0, 2), cell_id(1, 1), cell_id(2, 0)];
    const winning_conditions = [...rows, ...columns, diagonal_1, diagonal_2];

    function player_won(player_plays) {
        return winning_conditions.some(win_condition => win_condition.every(cell => player_plays.includes(cell)));        
    }

    $: x_won = player_won(plays[player_x]);
    $: o_won = player_won(plays[player_o]);
    $: game_over = x_won || o_won || all_plays.length == 9;

    function set_place(i, j) {
        plays[current_player] = [...plays[current_player], `${i}${j}`];
        plays = plays;
    }

    $: cell_values = [0, 1, 2].map(i => [1, 2, 3].map(j => cell_value(plays, i, j)));

    function cell_value(plays, i, j) {
        if (plays[player_x].includes(cell_id(i, j))) return player_x;
        if (plays[player_o].includes(cell_id(i, j))) return player_o;
        return '_';
    }

    function restart() {
        plays[player_x] = [];
        plays[player_o] = [];
    }
</script>

<h1>Tic Tac Toe</h1>

{#if x_won } <h2>X won!</h2> {/if}
{#if o_won } <h2>O won!</h2> {/if}
{#if game_over } <button on:click={restart}>Restart</button> {/if}

<div>
    <button disabled={all_plays.includes(cell_id(0, 0)) || game_over} on:click={() => set_place(0, 0)}>{cell_value(plays, 0, 0)}</button>
    <button disabled={all_plays.includes(cell_id(0, 1)) || game_over} on:click={() => set_place(0, 1)}>{cell_value(plays, 0, 1)}</button>
    <button disabled={all_plays.includes(cell_id(0, 2)) || game_over} on:click={() => set_place(0, 2)}>{cell_value(plays, 0, 2)}</button>
</div>
<div>
    <button disabled={all_plays.includes(cell_id(1, 0)) || game_over} on:click={() => set_place(1, 0)}>{cell_value(plays, 1, 0)}</button>
    <button disabled={all_plays.includes(cell_id(1, 1)) || game_over} on:click={() => set_place(1, 1)}>{cell_value(plays, 1, 1)}</button>
    <button disabled={all_plays.includes(cell_id(1, 2)) || game_over} on:click={() => set_place(1, 2)}>{cell_value(plays, 1, 2)}</button>
</div>
<div>
    <button disabled={all_plays.includes(cell_id(2, 0)) || game_over} on:click={() => set_place(2, 0)}>{cell_value(plays, 2, 0)}</button>
    <button disabled={all_plays.includes(cell_id(2, 1)) || game_over} on:click={() => set_place(2, 1)}>{cell_value(plays, 2, 1)}</button>
    <button disabled={all_plays.includes(cell_id(2, 2)) || game_over} on:click={() => set_place(2, 2)}>{cell_value(plays, 2, 2)}</button>
</div>