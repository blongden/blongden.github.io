<script>
    import Calc from '$lib/Calc.svelte'
    let uk_ni_2425 = {
        'tax_free': 1257000,
        'rates': [
            {
                'rate': 10,
                'lower': 1257000,
                'upper': 5026800
            },
            {
                'rate': 2,
                'lower': 5026800,
            }
        ]
    }
    let uk_ni_2324 = {
        'tax_free': 1257000,
        'rates': [
            {
                'rate': 12,
                'lower': 1257000,
                'upper': 5026800
            },
            {
                'rate': 2,
                'lower': 5026800,
            }
        ]
    }
    let scotland_tax_2324 = {
        'tax_free': 1257000,
        'tax_free_taper': 10000000,
        'rates': [
            {
                'rate': 19,
                'lower': 1257000,
                'upper': 1473200
            },
            {
                'rate': 20,
                'lower': 1473200,
                'upper': 2568800
            },
            {
                'rate': 21,
                'lower': 2568800,
                'upper': 4366200
            },
            {
                'rate': 42,
                'lower': 4366200,
                'upper': 12514000,
                'higher': true
            },
            {
                'lower': 12514000,
                'rate': 47
            }
        ]
    }
    let scotland_tax_2425 = {
        'tax_free': 1257000,
        'tax_free_taper': 10000000,
        'rates': [
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
    }

    let uk_tax_2425 = {
        'tax_free': 1257000,
        'tax_free_taper': 10000000,
        'rates': [
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
            }
        ]
    }

    let salary = 0
    let salary_t = ""

    let scotland_tax_2425_total 
    let scotland_tax_2324_total 
    let uk_tax_2425_total
    let scotland_tax_1pc_2425_total
    let uk_ni_2425_total
    let uk_ni_2324_total
    let uk_ni_1pc_2425_total

    let period = "Yearly"

    let GBP = new Intl.NumberFormat('en-GB', {
        style: 'currency',
        currency: 'GBP',
    });

    function setSalary() {
        salary = salary_t
    }

    function fc(valuep) {
        let chunks = 1
        switch(period) {
            case "Monthly": 
                chunks = 12;
                break;
            case "Weekly": 
                chunks = 52;
                break;
            case "Daily":
                chunks = 365;
                break;
        }
        return GBP.format(Math.abs(valuep/100)/chunks)
    }
</script>
<div class="container">
    <h1>Tax Calculator 24/25</h1>
    <label for="salary">
        <input id="salary" bind:value={salary_t} placeholder="Enter your annual salary"/>
    </label>
    <button on:click={setSalary}>Calculate</button>
</div>
<div class="container">
{#if (salary > 0)}
    {#key [period, salary]}
    <p>
        {#if scotland_tax_2425_total > 0}
            You will pay {fc(scotland_tax_2425_total - uk_tax_2425_total)} {#if scotland_tax_2425_total - uk_tax_2425_total > 0}more{:else}less{/if} tax
            to live in Scotland then you would if you lived elsewhere in the UK. You will pay 
            {fc(scotland_tax_2425_total - scotland_tax_2324_total)} {#if (scotland_tax_2425_total > scotland_tax_2324_total)}more{:else}less{/if}
            tax than last year, though if you have had a pay rise your tax will rise accordingly.
        {:else}
            Your income is under the current tax free allowance. You will not pay tax in Scotland or in the rest of the UK.
        {/if}
    </p>

    <p>
        You will pay {fc(uk_ni_2425_total)} in National Insurance, {fc(uk_ni_2425_total - uk_ni_2324_total)} {#if uk_ni_2425_total - uk_ni_2324_total > 0}more{:else}less{/if}
        than last year.
    </p>

    <p>
        A 1% increase in salary sacrifice or pension contribution would be worth {fc(salary)}, save you {fc(scotland_tax_2425_total - scotland_tax_1pc_2425_total)}
        in tax, {fc(uk_ni_2425_total - uk_ni_1pc_2425_total)} in National Insurance, and cost
        {fc((salary)-(scotland_tax_2425_total - scotland_tax_1pc_2425_total)-(uk_ni_2425_total - uk_ni_1pc_2425_total))} from your net salary.
    </p>

    <div style="text-align: center">
    {#each ['Yearly', "Monthly", "Weekly", 'Daily'] as period_t}
    <label style="padding: 1em; display: inline-block">
        <input type="radio" name="period" value={period_t} bind:group={period}>
        {period_t}
    </label>
    {/each}
    </div>

    <Calc bind:total={scotland_tax_1pc_2425_total} rates={scotland_tax_2425} salaryp={salary * 99} fc={fc} hidden=true>99% Scotland</Calc>
    <Calc bind:total={scotland_tax_2425_total} rates={scotland_tax_2425} salaryp={salary * 100} fc={fc}>Scotland 24/25</Calc>
    <Calc bind:total={scotland_tax_2324_total} rates={scotland_tax_2324} salaryp={salary * 100} fc={fc}>Scotland 23/24</Calc>
    <Calc bind:total={uk_tax_2425_total} rates={uk_tax_2425} salaryp={salary * 100} fc={fc}>UK 24/25</Calc>
    <Calc bind:total={uk_ni_2425_total} rates={uk_ni_2425} salaryp={salary * 100} fc={fc}>UK National Insurance 24/25</Calc>
    <Calc bind:total={uk_ni_1pc_2425_total} rates={uk_ni_2425} salaryp={salary * 99} fc={fc} hidden=true>99% UK National Insurance 24/25</Calc>
    <Calc bind:total={uk_ni_2324_total} rates={uk_ni_2324} salaryp={salary * 100} fc={fc}>UK National Insurance 23/24</Calc>
    {/key}
{/if}
    <p>Made in 🏴󠁧󠁢󠁳󠁣󠁴󠁿 by <a href="https://x.com/blongden">@blongden</a>. This site proudly uses no cookies, collects no data and does not track you at all.</p>
</div>