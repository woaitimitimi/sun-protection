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
      <div class="box_0">
        <div class="btn" @click="handleRoute('locator')">
          <svg class="icon" aria-hidden="true">
            <use xlink:href="#icon--backhand-index-p"></use>
          </svg>
          <p>👉 Click here for your current UV Risk (& more!)</p>
        </div>
      </div>
      <div class="box_1">
        <div class="left">
          <p class="header">Hello Sunshine :)</p>
          <p class="title">
            UV exposure affects averyone, no mattar what your age, location, or skin tone are..😞
          </p>
          <p class="title">Learn how to protect your beautiful skin today!</p>
          <p class="title">TAN SAFE LOVE XX</p>
        </div>
        <div class="right">
          <p class="header">We hate to break it to you, but...</p>
          <p class="title">🌞 2 in 3 Australians will be diagnosed with skin cancer by age 70.</p>
          <p class="title">
            🌞 People with darker skin tones are also at risk and may not showearly symptoms.
          </p>
          <p class="title">
            🌞 Prolonged sun exposure can cause premature aging, sunburns,and serious health risks.
          </p>
        </div>
      </div>
      <div class="box_2">
        <div class="left">
          <p class="tip">Major City's UV Index NOW</p>
          <div class="header">
            <div class="item" v-for="item in headerList">
              {{ item.title }}
            </div>
          </div>
          <div class="list">
            <div class="item" v-for="item in dataList">
              <p>{{ item.type }}</p>

              <div class="btn" @click="handleRoute('locator')">
                <svg class="icon" aria-hidden="true">
                  <use xlink:href="#icon--backhand-index-p"></use>
                </svg>
                <p>👉 Click here for more!</p>
              </div>
            </div>
          </div>
        </div>
        <div class="right">
          <img src="../assets/2.jpg" alt="" />
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
const headerList = ref([
  {
    title: 'STATE',
  },
  {
    title: 'UV',
  },
])
const dataList = ref([
  {
    type: 'Melbourne',
  },
  {
    type: 'Sydney',
  },
  {
    type: 'Canberra',
  },
  {
    type: 'Perth',
  },
])
//
const $router = useRouter()
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
      width: 1350px;
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

    .box_0 {
      display: flex;
      justify-content: center;

      .btn {
        width: fit-content;
        border: 1px solid #ddd;
        padding: 10px 20px;
        border-radius: 50px;
        cursor: pointer;
        display: flex;
        align-items: center;
        gap: 15px;

        svg {
          width: 30px;
          height: 30px;
        }
      }
    }

    .box_1 {
      padding: 20px;
      border-bottom: 1px solid #ddd;
      display: grid;
      grid-template-columns: repeat(2, 1fr);

      .left,
      .right {
        display: flex;
        flex-direction: column;
        gap: 20px;

        .header {
          font-size: 20px;
          font-weight: bold;
        }
      }
    }

    .box_2 {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 20px;
      gap: 30px;
      width: 100%;
      overflow: hidden;
      padding: 20px;
      align-items: center;

      .left {
        display: flex;
        flex-direction: column;
        gap: 15px;

        .header {
          display: grid;
          grid-template-columns: repeat(2, 1fr);
          place-items: center;
        }

        .list {
          display: flex;
          flex-direction: column;
          gap: 15px;

          .tip {
            font-size: 20px;
            font-weight: bold;
          }

          .item {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            /* place-items: center; */

            .btn {
              border: 1px solid #ddd;
              padding: 10px 20px;
              border-radius: 50px;
              cursor: pointer;
              display: flex;
              align-items: center;
              gap: 15px;

              svg {
                width: 30px;
                height: 30px;
              }
            }
          }
        }
      }

      .right {
        width: 100%;

        img {
          width: 100%;
        }
      }
    }
  }
}
</style>
