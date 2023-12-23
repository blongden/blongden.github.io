<script>
export let rates = {'rates': [], 'tax_free': 0, 'tax_free_taper': 0}
export let salaryp = 0
let taxes = []
export let total = 0
export let hidden = false
export let fc

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
if (rates.tax_free_taper && salaryp > rates.tax_free_taper) {
    taxfreelossp = (salaryp - rates.tax_free_taper) / 2
    if (taxfreelossp > rates.tax_free) {
        taxfreelossp = rates.tax_free
    }
}

total = 0
taxes = []
rates.rates.forEach(rate => {
    var i = calculateTaxableValue(salaryp, taxfreelossp, rate)
    total += i.tax
    if (i.tax > 0) {
        taxes.push(i)
    }
})
</script>
{#if hidden == false && total > 0}
<table style='flex-grow: 1; min-width: 350px; padding: 1em'>
<caption><slot/></caption>
<tr>
    <th>Rate</th>
    <th>Income in bracket</th>
    <th>Tax to pay</th>
</tr>
<tr>
    <td>0%</td>
    <td>To {fc(rates.tax_free - taxfreelossp)}</td>
    <td>£0.00</td>
</tr>
{#each taxes as tax}
<tr>
    <td>{tax.rate}%</td>
    <td>{#if tax.upper === undefined || tax.upper < tax.lower}0{:else}{fc(tax.lower)} to {fc(tax.upper)}{/if}</td>
    <td>{#if tax.tax === undefined}0{:else}{fc(tax.tax)}{/if}</td>
</tr>
{/each}
<tr>
    <td>Total</td>
    <td></td>
    <td>{fc(total)}</td>
</tr>
</table>
{/if}