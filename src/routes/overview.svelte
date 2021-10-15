<script context="module">
    export async function preload({ params }, { token }) {
        if (!token) {
            this.redirect(302, '/login');
        }
    }
</script>
<script>
    import {post} from 'utils.js';

    async function getMyData(){
        return await post(`auth/getMyData`).then(r=>{
            r.funds = r.accounts.reduce(
                ( funds, account ) => funds + account.balance,
                0
            )
            return r
        })
    }
    async function getTransaction(){
        return await post(`auth/getTransactions`)
    }
</script>
{#if process.browser}
    {#await getMyData()}
        Loading...
    {:then my}
        <section>
            <p>{my.name}</p>
        </section>
        <section>
            My funds
            <p style="font-size: xx-large">{my.funds}</p>
        </section>
        <section>
            <ul>
                {#each my.accounts as account}

                    <li>{account.number} ({account.name})</li>

                {/each}
            </ul>
        </section>
        <section>
            {#await getTransaction()}
                Loading...
            {:then transaction}
                <table class="table table-striped table-border">
                    <thead>
                    <tr>
                        <th>senderName</th>
                        <th>amount</th>
                        <th>createdAt</th>
                        <th>status</th>

                    </tr>
                    </thead>
                    <tbody>
                    {#each transaction as transaction}
                        <tr>
                            <td><b>{transaction.senderName}</b>{transaction.explantion}</td>
                            <td style="color: {transaction.amount >=0 ?'green': 'red'}">{transaction.currency}</td>
                            <td>{transaction.createAt}</td>
                            <td><b>{transaction.status}</b>{transaction.statusDetail}</td>


                        </tr>
                    {/each}
                    </tbody>
                </table>

                <pre>{JSON.stringify(transaction, null, 4)}</pre>
            {/await}
        </section>
        <!--<pre>{JSON.stringify(my, null, 4)}</pre> выводит данные json-->
    {/await}
{/if}