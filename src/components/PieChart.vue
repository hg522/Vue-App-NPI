<script>
    import { Pie,mixins } from 'vue-chartjs'
    const { reactiveProp } = mixins

    export default {
        extends: Pie,
        mixins: [reactiveProp],
        props: ['options'],
        data(){
            return{

            }
        },
        mounted () {

            this.renderChart(this.chartData, this.options)
        }
    }
</script>

<style>
</style>