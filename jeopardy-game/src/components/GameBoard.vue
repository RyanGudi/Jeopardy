'<template>
    <div class="table">
        <thead>
            <tr>
                <th v-for="(category, index) in categories" :key="index">{{ category }}</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="(row, idx) in rows" :key="idx">
                <td v-for="(score, index) in row.scores" :key="index">{{ score }}</td>
            </tr> 
        </tbody>
        <button type ="button" @click="fetchData">Button</button>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                categories: ['Math','Science','Reading','Music'],
                rows: [
                { scores: ['100', '100', '100', '100'] },
                { scores: ['200', '200', '200', '200'] },
                { scores: ['300', '300', '300', '300'] }
                ],
            };
        },
        methods: {
            async fetchData() {
                try {
                    const apiUrl = 'https://opentdb.com/api.php?amount=4&type=boolean'
                    const response = await fetch(apiUrl);
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }
                    console.log(response);
                } catch(error) {
                    console.error('Error fetching data', error);
                }
            }
        }
    }
</script>'