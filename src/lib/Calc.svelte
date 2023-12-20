<script>
    export let rates = []
    export let salaryp = 0
    let taxes = []
    export let total = 0
    export let currency
    export let hidden = false

    function calculateTaxableValue(salaryp, taxfreelossp, rate) {
        var i = { ...rate }
        if (i.upper !== undefined) {
            i.upper = Math.min(salaryp, i.upper)
        } else {
            i.upper = salaryp
        }

        if (i.higher) {
            i.lower = i.lower - taxfreelossp
        }

        let tax = 0
        if (i.upper - i.lower > 0) {
            tax = i.rate/100 * (i.upper - i.lower)
        }
        i.tax = tax
        return i      
    }


    let taxfreelossp = 0
    {
        if (salaryp > 10000000) {
            taxfreelossp = (salaryp - 10000000) / 2
        }
        total = 0
        taxes = []
        rates.forEach(rate => {
            var i = calculateTaxableValue(salaryp, taxfreelossp, rate)
            total += i.tax
            if (i.tax > 0) {
                taxes.push(i)
            }
        })
    }

</script>
{#if hidden == false && total > 0}
<table style='flex-grow: 1; min-width: 350px; padding: 1em'>
<caption><slot/></caption>
<tr>
    <th>Rate</th>
    <th>Income in bracket</th>
    <th>Tax to pay</th>
</tr>
{#each taxes as tax}
<tr>
    <td>{tax.rate}%</td>
    <td>{#if tax.upper === undefined || tax.upper < tax.lower}0{:else}{currency.format((tax.upper - tax.lower)/100)}{/if}</td>
    <td>{#if tax.tax === undefined}0{:else}{currency.format(tax.tax/100)}{/if}</td>
    <td></td>
</tr>
{/each}
<tr>
    <td></td>
    <td></td>
    <td>{currency.format(total/100)}</td>
</tr>
</table>
{/if}