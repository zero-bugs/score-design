<template>
  <base-box :headerBorder=false title="曲谱搜索" type="primary">
    <template v-slot:title-addon>
      <div class="search-model">
        <el-input v-model="info" class="search-info" placeholder="请输入搜索内容">
          <el-button slot="append" class="search-button" icon="el-icon-search" native-type="submit"
                     @click="search(info)"></el-button>
        </el-input>
      </div>
    </template>
    <div v-show="this.showResult" class="search-list">
      <a
        v-for="score in scores"
        :key="score.id"
        class="score-item"
        @click="$router.push({name: 'score-detail', params: {id: score.id}})">
        <img :alt="score.name" :src="score.poster"/>
        <p><strong class="name">{{ score.name }} </strong>
          <strong class="keys">{{ score.keys }}</strong></p>
      </a>
    </div>
  </base-box>

</template>

<script>
import ScoreService from 'services/ScoreService'

export default {
  data () {
    return {
      showResult: false,
      scores: [],
      info: ''
    }
  },
  methods: {
    async search (info) {
      if (info) {
        let res = await ScoreService.search(info)
        this.scores = res.data.scores
        this.showResult = true
      } else {
        this.$message({
          message: '搜索内容为空，请重新输入',
          type: 'warning',
          duration: 1500
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.search-model {
  margin: auto;
  width: 500px;
  margin-top: 50px;
  margin-bottom: 50px;

  .search-info {
    border-right: none !important;
  }

  .search-button {
    border: none;
    background-color: white;
  }
}

.search-list {
  .score-item {
    display: block;
    margin: 10px;
    margin-top: 0;
    float: left;
    font-size: 13px;
    width: 230px;
    cursor: pointer;

    img {
      // height: 160px;
      // width: 100%;
      // object-fit: cover;
      display: block;
      height: 142px;
      width: 230px;
      border-radius: 3px;
    }

    p {
      text-align: center;

      .name {
        font-size: 13px;
      }

      .keys {
        color: #e09015;
      }
    }
  }
}
</style>
