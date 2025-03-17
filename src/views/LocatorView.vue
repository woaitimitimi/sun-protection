<template>
    <div class="container">
        <header>
            <div class="banner">
                <img src="../assets/1.jpg" alt="" />
            </div>
            <div class="header_box">
                <div class="btn" @click="handleRoute('')">HOME</div>
                <div class="btn" @click="handleRoute('locator')">LOCATOR</div>
            </div>
        </header>
        <main>
            <div class="search">
                <div class="btn">Search</div>
                <input type="text" @keydown.enter="keyDown" placeholder="Users can enter their location"
                    v-model="params.location" />
            </div>
            <div class="panels">
                <div class="uv">
                    <div class="tip">UV LEVELS</div>
                    <div id="chart-container" style="width: 600px; height: 400px"></div>
                    <div class="count">
                        <div>Current UV Level:{{ uvParams.uv }}</div>
                        <div class="">Max: {{ uvParams.uv_max }}</div>
                    </div>
                </div>
                <div class="tips">
                    <div class="tip">Sun Safety Tips</div>
                    <div class="">{{ uvParams.tips }}</div>
                    <div class="skin">
                        <div class="header">
                            <div class="item" v-for="item in headerList">
                                {{ item.title }}
                            </div>
                        </div>
                        <div class="list">
                            <div class="item" v-for="item in dataList">
                                <p>{{ item.type }}</p>
                                <p class="color" :style="{ background: item.color }"></p>
                                <p class="time">{{ item.time }}</p>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </main>
    </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import VChart from '@visactor/vchart'
import { useRouter } from 'vue-router'
//
const $router = useRouter()
//
const params = ref({
    location: '',
    lat: -37.8142454,
    lng: 144.9631732,
    alt: 100,
})

let uvParams = ref({
    uv: 0,
    ux_max: 0,
    tips: '',
})
//
const headerList = ref([
    {
        title: 'Skin Type',
    },
    {
        title: 'Skin COLOUR',
    },
    {
        title: 'RECOMMENDED SUNEXPOSURE TIME (MIN)',
    },
])
const dataList = ref([
    {
        type: 'Light, Pale White',
        color: '#f5d2b6',
        time: 0,
    },
    {
        type: 'White Fair',
        color: '#e7b594',
        time: 0,
    },
    {
        type: 'Medium, White To Olive',
        color: '#d4a385',
        time: 0,
    },
    {
        type: 'Olive, Moderate Brown ',
        color: '#d38860',
        time: 0,
    },
    {
        type: 'Brown, Dark Brown ',
        color: '#a45f42',
        time: 0,
    },
    {
        type: 'Black, Very Dark, Brown To Black ',
        color: ' #3b1d1b',
        time: 0,
    },
])

let vchart = ref(null)
let chartData = ref([])

//
onMounted(async () => {
    await getLocation()
})
//
const getLocation = async () => {
    if (!params.value.location) {
        getUv()
        return
    }
    fetch(
        `https://geocode.maps.co/search?q=${params.value.location}&api_key=67d155721e06d762905832yce7a68b2`,
    )
        .then((res) => res.json())
        .then((res) => {
            console.log(res)

            if (res.length > 0) {
                console.log(res)

                params.value.lat = Number(res[0].lat)
                params.value.lng = Number(res[0].lon)
                getUv()
            }
        })
}
//
const getUv = async () => {
    const apiKey = 'openuv-h8j7rm84fjsu1-io'
    console.log(params.value.lat)

    const lat = params.value.lat
    const lng = params.value.lng
    const alt = params.value.alt
    const url = `https://api.openuv.io/api/v1/uv?lat=${lat}&lng=${lng}&alt=${alt}`
    const headers = {
        'x-access-token': apiKey,
    }

    fetch(url, { headers })
        .then((response) => response.json())
        .then((res) => {
            if (res.result) {
                //
                if (vchart.value) {
                    const chartContainer = document.getElementById('chart-container')
                    if (chartContainer) {
                        chartContainer.removeChild(chartContainer.firstChild)
                    }
                }
                uvParams.value.uv = res.result.uv
                uvParams.value.uv_max = res.result.uv_max
                chartData.value = [
                    {
                        type: 'Min',
                        value: res.result.uv,
                    },
                ]
                dataList.value.forEach((data, index) => {
                    console.log(' ', res.result.safe_exposure_time)
                    data.time = res.result.safe_exposure_time[`st${index + 1}`] ?? 0
                })

                getSunProtectionTips(res.result.uv)
                draw()
            }
        })
        .catch((error) => {
            console.error('error:', error)
            chartData.value = [
                {
                    type: 'Max',
                    value: 0,
                },
            ]

            getSunProtectionTips(0)
            draw()
        })

    // test data
    // uvParams.value.uv = 2
    // uvParams.value.uv_max = 5
    // chartData.value = [
    //   {
    //     type: 'Min',
    //     value: 2,
    //   },
    // ]
    // let safe_exposure_time = {
    //   str1: 55,
    //   str2: 66,
    //   str3: 88,
    //   str4: 110,
    //   str5: 175,
    //   str6: 329,
    // }
    // dataList.value.forEach((data, index) => {
    //   data.time = safe_exposure_time[`str${index + 1}`] ?? 0
    // })

    // getSunProtectionTips(uvParams.value.uv)
    // draw()
}
// enter
const keyDown = async (e) => {
    if (e.keyCode == 13) {
        await getLocation()
    }
}
//
function getSunProtectionTips(uvIndex) {
    let tips = ''
    if (uvIndex <= 2) {
        tips =
            'Low UV intensity. When going out, you can choose to apply low - factor sunscreen (such as SPF15 - 30) and wear a hat.'
    } else if (uvIndex <= 5) {
        tips =
            'Moderate UV intensity. It is recommended to apply sunscreen with SPF30 - 50, wear a sun hat and sunglasses, and try to stay in the shade.'
    } else if (uvIndex <= 7) {
        tips =
            'High UV intensity. High - factor sunscreen (SPF50+) is required. Wear sun - protective clothing, a wide - brimmed sun hat, and sunglasses. Try to avoid being outdoors for long periods from 10 am to 4 pm.'
    } else if (uvIndex <= 10) {
        tips =
            'Very high UV intensity. In addition to applying high - factor sunscreen and taking full body protection measures, minimize outdoor time as much as possible. If you need to go out, be sure to find a shaded area.'
    } else {
        tips =
            'Extremely high UV intensity, dangerous level. Try not to go out. If you must go out, take comprehensive protection, including applying high - factor sunscreen, wearing sun - protective clothing, a sun hat, sunglasses, and using a sunshade umbrella, etc.'
    }
    uvParams.value.tips = tips
    return tips
}
// draw
const draw = () => {
    const spec = {
        type: 'gauge',
        data: [
            {
                id: 'pointer',
                values: chartData.value,
            },
            {
                id: 'segment',
                values: [
                    {
                        type: 'Level 1',
                        color: '#07A35A',
                        value: 2,
                    },
                    {
                        type: 'Level 2',
                        color: '#FFC528',
                        value: 5,
                    },
                    {
                        type: 'Level 3',
                        color: '#E33232',
                        value: 7,
                    },
                    {
                        type: 'Level 4',
                        color: 'purple',
                        value: 10,
                    },
                ],
            },
        ],
        gauge: {
            type: 'gauge',
            dataIndex: 1,
            categoryField: 'type',
            valueField: 'value',
            seriesField: 'type',
            segment: {
                style: {
                    cornerRadius: 10,
                    fill: (datum) => datum['color'],
                },
            },
            label: {
                visible: true,
                position: 'inside-outer',
                offsetRadius: 10,
                style: {
                    text: (datum) => datum['type'],
                },
            },
        },
        pointer: {
            style: {
                fill: '#666666',
            },
        },
        categoryField: 'type',
        valueField: 'value',
        outerRadius: 0.9,
        innerRadius: 0.6,
        startAngle: -180,
        endAngle: 0,
        centerY: '100%',
        layoutRadius: 'auto',
        axes: [
            {
                type: 'linear',
                orient: 'angle',
                inside: true,
                grid: { visible: false },
            },
        ],
    }

    vchart.value = new VChart(spec, { dom: 'chart-container' })
    vchart.value.renderSync()
}
// route
const handleRoute = (url) => {
    $router.push(`/${url}`)
}
</script>

<style lang="scss" scoped>
.container {
    display: flex;
    flex-direction: column;
    gap: 20px;

    header {
        padding: 20px;
        display: flex;
        flex-direction: column;
        gap: 10px;

        .banner {
            margin: auto;
            width: 1250px;
            display: flex;
            justify-content: center;
            align-items: baseline;
            gap: 20px;

            img {
                margin: auto;
                width: 100%;
                aspect-ratio: 16/2;
                object-fit: cover;
            }
        }

        .header_box {
            margin: auto;
            width: 1250px;
            display: flex;
            justify-content: center;
            align-items: baseline;
            gap: 20px;

            .btn {
                cursor: pointer;
                color: rgb(177, 154, 133);
                font-weight: bold;
            }

            .tip {
                font-size: 20px;
            }
        }
    }

    main {
        width: 1250px;
        margin: auto;
        display: flex;
        flex-direction: column;
        gap: 20px;

        .search {
            display: flex;
            gap: 20px;
            height: 50px;

            .btn {
                width: 100px;
                text-align: center;
                background-color: orangered;
                color: white;
                cursor: pointer;
                line-height: 50px;
            }

            input {
                all: unset;
                flex: 1;
                border: 1px solid #ddd;
                padding: 0 20px;
            }
        }

        .panels {
            display: grid;
            grid-template-columns: 2fr 3fr;
            gap: 20px;

            .uv {
                padding: 20px;
                border: 1px solid #ddd;
                display: flex;
                flex-direction: column;
                gap: 30px;

                .tip {
                    text-align: center;
                    font-weight: bold;
                }

                .count {
                    align-self: center;
                    display: flex;
                    flex-direction: column;
                    align-items: center;
                    font-size: 22px;
                }
            }

            .tips {
                padding: 20px;
                border: 1px solid #ddd;

                .tip {
                    text-align: center;
                    font-weight: bold;
                }

                .skin {
                    display: flex;
                    flex-direction: column;
                    gap: 10px;
                    width: 100%;
                    /* background-color: pink; */
                    padding: 20px;

                    .header {
                        display: grid;
                        grid-template-columns: repeat(3, 1fr);
                        place-items: center;
                    }

                    .list {
                        display: flex;
                        flex-direction: column;
                        gap: 15px;

                        .item {
                            display: grid;
                            grid-template-columns: repeat(3, 1fr);

                            /* place-items: center; */
                            .color,
                            .time {
                                place-self: center;
                            }

                            .color {
                                width: 200px;
                                height: 50px;
                            }
                        }
                    }
                }
            }
        }
    }
}
</style>