    <template>
    <div class="container container-fluid">
        <b-jumbotron>

            <img id="imgSource"/>
        <canvas id="canvas" ref="canvas">
        </canvas>

            <hr>
            <div class="text-center">
            <b-form-file
                    :state="Boolean(file)"
                    placeholder="Choose a file..."
                    drop-placeholder="Drop file here..."
                    @change="onFileChange"
            />
    <b-button @click="predictResult" size="lg" variant="danger">Find Objects</b-button>
            </div>
        </b-jumbotron>
    </div>
</template>

<script>
    import * as cocoSsd from '@tensorflow-models/coco-ssd';

    export default {
        name: "Home",
        data: function () {
            return {
                url: null,
                test: '',
                image: null,
                result: null,
            }
        },
        methods: {
            onFileChange(e) {
                const file = e.target.files[0];
                console.log(file)
                this.url = URL.createObjectURL(file);
                var canvasElement = document.getElementById('canvas')
                var imageElement = document.getElementById('imgSource')

                var ctx = canvasElement.getContext('2d')

                console.log(canvasElement.height)
                console.log(canvasElement.width)
                var image = new Image()
                image.src = this.url
                image.onload = () => {
                    ctx.drawImage(image, 0, 0, canvasElement.width, canvasElement.height,)
                }
            },
            predictResult: async function () {
                console.log('predict result called')
                const model = await cocoSsd.load();
                console.info(document.getElementById('canvas'))
                const predictions = await model.detect(document.getElementById('canvas'));
                this.result = predictions
                let classArray = []
                for(let someResult of this.result){
                    console.info(someResult)
                    await this.fillRectangle(someResult)
                    classArray.push(someResult.class)
                }

            this.$toast.open({
                message: `Found Objects are: ${classArray}`,
                type:'is-danger',
            })
            },
            fillRectangle: async  function(prediction){
                const x = prediction.bbox[0];
                const y = prediction.bbox[1];
                const width = prediction.bbox[2];
                const height = prediction.bbox[3];

                var canvasElement =  document.getElementById('canvas')
                var context = canvasElement.getContext('2d')

                context.beginPath()
                context.rect(x, y, width, height)
                context.lineWidth = 2
                context.fillStyle = "red"
                context.fillText(prediction.class, prediction.bbox[0] , prediction.bbox[1])
                context.strokeStyle = 'red'
                context.stroke()
            }
        }
    }
</script>

<style scoped>
canvas {
    background-color: darkcyan;
    height: auto                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    ;
    width: 100%;
}
</style>