<script>
    import Calc from '$lib/Calc.svelte'
    let scotland_year2425 = [
        {
            'rate': 19,
            'lower': 1257000,
            'upper': 1487600
        },
        {
            'rate': 20,
            'lower': 1487600,
            'upper': 2656100
        },
        {
            'rate': 21,
            'lower': 2656100,
            'upper': 4366200
        },
        {
            'rate': 42,
            'lower': 4366200,
            'upper': 7500000
        },
        {
            'rate': 45,
            'lower': 7500000,
            'upper': 12514000,
            'higher': true
        },
        {
            'lower': 12514000,
            'rate': 48
        }
    ]

    let uk_year2425 = [
        {
            'rate': 20,
            'lower': 1257000,
            'upper': 5027000
        },
        {
            'rate': 40,
            'lower': 5027000,
            'upper': 12514000,
            'higher': true
        },
        {
            'rate': 45,
            'lower': 12514000
        },
    ]

    let salary = 0
    let salary_t = 0

    let scotland_total 
    let uk_total
    let onepc_scotland_total

    let GBP = new Intl.NumberFormat('en-GB', {
        style: 'currency',
        currency: 'GBP',
    });

    function setSalary() {
        salary = salary_t
    }

</script>
<h2>Tax Calculator 24/25</h2>
<div class="container">
    <label for="salary">
        Salary
        <input id="salary" bind:value={salary_t}/>
    </label>
    <button style='width: 250px'on:click={setSalary}>Calculate</button>
</div>

<div class="container">
    <p>
        {#if scotland_total - uk_total > 0}
            You will pay {GBP.format((scotland_total - uk_total)/100)} more tax to live in in Scotland then you would
            if you lived elsewhere in the UK.
        {:else if uk_total - scotland_total > 0}
            You will pay {GBP.format((uk_total - scotland_total)/100)} less tax to live in Scotland then you would
            if you lived elsewhere in the UK.
        {:else}
            Your income is under the current tax free allowance. You will not pay tax in Scotland or in the rest of the UK.
        {/if}
    </p>

{#if salary > 100000}
<p>
    Your salary is over £100,000. For every £2 you earn over £100k you lose £1 of your tax free allowance, which
    is then taxed at the higher rate (resulting in a marginal rate of up to 68%). You can use salary sacrifice or
    increase pension contributions to keep your taxable salary at under £100,000 so you keep more of your money at a
    relatively low cost.
</p>
{/if}


<p>
    A 1% increase in salary sacrifice or pension contribution would save you {GBP.format((scotland_total - onepc_scotland_total)/100)}
    in tax, and cost {GBP.format(salary * 0.01)} ({GBP.format((salary * 0.01)-(scotland_total - onepc_scotland_total)/100)} net)
</p>
</div>

{#key salary}
<div class="container" style='display: flex; flex-wrap: wrap;'>
<Calc bind:total={onepc_scotland_total} rates={scotland_year2425} salaryp={salary * 99} currency={GBP} hidden=true>99% Scotland</Calc>
<Calc bind:total={scotland_total} rates={scotland_year2425} salaryp={salary * 100} currency={GBP}>Scotland</Calc>
<Calc bind:total={uk_total} rates={uk_year2425} salaryp={salary * 100} currency={GBP}>UK</Calc>
</div>
{/key}

<p>Made in 🏴󠁧󠁢󠁳󠁣󠁴󠁿 by <a href="https://x.com/blongden">@blongden</a></p>