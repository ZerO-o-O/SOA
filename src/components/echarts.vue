<template>
<div class="graph">
    <!--    初始化echarts需要个有宽高的盒子-->
    <div style="width:40px;height:600px;font-size:40px;float:left;background-color: blue;color: white"> 便民功能</div>
    <div ref="mapbox" style="height:800px;width:1200px;margin:0 auto;"  ></div>
    <div></div>>
</div>
</template>

<script>
    // import echarts from 'echarts'
    // import 'echarts/map/js/china'

    import axios from 'axios'

    const option={
        title:{
            text:"疫情地图",
        },
        series:[{
            name:"现存确诊人数",
            type:'map', //告诉echarts要去渲染一个地图
            map:'china',
            label:{
                show:true, // 控制对应地区的汉字
                color:'#333',
                fontSize:10,
            },
            roam:true, //控制地图放大缩小
            zoom:1.2 ,//控制地图的放大缩小
            data:[] ,//用来展示后台给的数据  {name:xx,value:xxx}
            /*      控制地图板块的颜色和边框*/
            itemStyle:{
                areaColor:'#eee',
                borderColor:"green"  //边框颜色
            },
            /*     控制鼠标滑过之后的背景色和字体颜色*/
            emphasis:{
                label:{
                    color:'#fff',
                    fontsize:12
                },
                itemStyle: {
                    areaColor: '#83b5e7'
                }
            }

        }
        ],
        visualMap:[{
            type:'piecewise',
            show:true,
            // splitNumber:4,
            pieces:[
                //分段
                {min:10000},
                {min:1000,max:9990},
                {min:100,max:999},
                {min:10,max:99},
                {min:1,max:9},
                {min:0, max:0}
            ],
            // align:'right'
            //orient:'horizontal' 默认竖直
            //left right 这些属性控制分段所在的位置
            //textStyle() 分段大小
            inRange:{
                symbol:'rect',
                color:['#ffc0b1','#9c0505']
            },
            itemWidth:20,
            itemHeight:10
        }],
        tooltip:{
            trigger:'item'  //鼠标移入后显示人数
        }
    }
    export default {
        name: 'echarts',
        mounted(){  //template挂载到页面时调用
            this.mychart = this.$echarts.init(this.$refs.mapbox);
            this.getData(); //执行getData方法
            // this.mychart.setOption(option);
        },
        methods:{
            getData(){
                axios.get('https://lab.isaaclin.cn/nCoV/api/area').then(res=>{
                    let data = [];
                    res.data.results.forEach(item=>{
                            if (item.countryEnglishName == "China"){
                                data.push({"name" : item.provinceShortName, "value": item.currentConfirmedCount})
                            }
                        }
                    );   //从接口获取到数据后，使用map()函数，进行接口数据映射
                    option.series[0].data = data;
                    this.mychart.setOption(option) //这行代码能执行的前提是DOM已经渲染完成，只有DOM已渲染完成才能echarts初始化
                })
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>