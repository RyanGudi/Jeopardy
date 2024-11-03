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
                categories: ['','','',''],
                rows: [
                { scores: ['100', '100', '100', '100'] },
                { scores: ['200', '200', '200', '200'] },
                { scores: ['300', '300', '300', '300'] }
                ],
                questions: ['','','','',
                            '','','','',
                            '','','',''
                            ]
            };
        },
        methods: {
            async fetchData() {
                try {
                    const categoryNum = Math.floor(Math.random() * 9) + 20;
                    const apiUrl = `https://opentdb.com/api.php?amount=4&category=${categoryNum}&type=boolean`;
                    const response = await fetch(apiUrl);
                    console.log(response.results);
                    const data = await response.json();
                    console.log(data);
                    console.log('Category', data.results[0].category);
                    if (!response.ok) {
                        throw new Error('Network response was not ok');
                    }

                    //this.processResults();
                } catch(error) {
                    console.error('Error fetching data', error);
                }
            },
            // processResults(results) {
            //     //this.categories = Array.from(new Set(results.map(result => result.category)));

            // }
        }
    }
</script>