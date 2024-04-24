<script>
import { onMount } from 'svelte';

  let fromCurrency = 'USD';
  let toCurrency = 'RUB';
  let amountFrom = 0;
  let amountTo = 0;
  let list = [];

  async function fetchData(currentCurrency) {
    const response = await fetch(`https://open.er-api.com/v6/latest/${currentCurrency}`);
    const data = await response.json();

    return data;
  }

  async function convertCurrency1(direction) {
    if (!fromCurrency || !toCurrency || (!amountFrom && !amountTo)) return;

    let data;
    let rate;

    if (direction === 'to') {
        if (!amountFrom) return;
        data = await fetchData(fromCurrency);
        rate = data.rates[toCurrency];
        if (!rate) {
            console.error(`Conversion rate from ${fromCurrency} to ${toCurrency} not available.`);
            return;
        }
        amountTo = (parseFloat(amountFrom) * rate).toFixed(2);
    } else if (direction === 'from') {
        if (!amountTo) return;
        data = await fetchData(toCurrency);
        rate = data.rates[fromCurrency];
        if (!rate) {
            console.error(`Conversion rate from ${toCurrency} to ${fromCurrency} not available.`);
            return;
        }
        amountFrom = (parseFloat(amountTo) * rate).toFixed(2);
    }
}

  async function mount() {
      const { rates } = await fetchData('USD')
      list = [...Object.keys(rates)]
  }

  function resetAmounts() {
    amountFrom = 0;
    amountTo = 0;
  }

    onMount(mount);

</script>

<main>
  <h1 style="color: #00CC00;">Currency converter service</h1>

  <h3>Please choose currencies, which you want to convert</h3>

  <div class="table">
    <table>
      <tr>
        <td class="currency">
          <select name="currencty" id="currencyID" bind:value={fromCurrency} on:input={resetAmounts}>
            {#each list as currency}
              <option value={currency}>{currency}</option>
            {/each}
          </select>
        </td>
        <td class="arrow">&#8660;</td>
        <td class="currency">
          <select name="currency" id="currencyID" bind:value={toCurrency} on:input={resetAmounts}> 
            {#each list as currency}
              <option value={currency}>{currency}</option>
            {/each}
          </select>
        </td>
      </tr>
      <tr>
        <td class="amount">
          <input type="number" id="amountID" bind:value={amountFrom} on:input={() => convertCurrency1('to')} >
        </td>
        <td class="arrow"></td>
        <td class="amount">
          <input type="number" id="amountID" bind:value={amountTo} on:input={() => convertCurrency1('from')}>
        </td>
      </tr>
    </table>
  </div>
</main>

<style>

  h1,h3{
    margin-bottom: 50px;
  }

  .table{
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .currency{
    width: 100px;
  }
  .arrow{
    width: 10px;
    padding-bottom: 10px;
  }
  .amount{
    width: 200px;
  }

  #amountID{
    text-align: center;
  }

  #currencyID{
    border-radius: 5px;
    border-width: 1px;
    padding: 5px;
    border-style:groove;
    background-color: #242424;

    margin-bottom: 10px;
  }

  #amountID{
    border-radius: 5px;
    border-width: 1px;
    background-color: transparent;
    border-style: solid;
    padding: 5px 0;
  }
</style>