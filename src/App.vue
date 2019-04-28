<template>
  <div id="app">
    <h1>ライバルアイドル火力計算機</h1>

    <ul>
      <li>ソースコード <a href="https://github.com/project-brightblue/wingcalc">https://github.com/project-brightblue/wingcalc</a></li>
      <li>ほとんどハリボテです</li>
      <li><a href="https://wikiwiki.jp/shinycolors/%E4%BB%95%E6%A7%98%E8%80%83%E5%AF%9F#e555fc6c">火力の計算</a>と<a href="https://wikiwiki.jp/shinycolors/%E5%90%84%E3%82%AA%E3%83%BC%E3%83%87%E3%82%A3%E3%82%B7%E3%83%A7%E3%83%B3%E5%80%8B%E5%88%A5%E6%94%BB%E7%95%A5">ライブの情報</a>はWikiのものを参考にしています</li>
      <li>謎バフについては検証中です</li>
    </ul>

    <!-- そのうちjsonからオーディションの情報を拾って生成させるようにする -->

    <select v-model="audition">
      <option disabled value="">シーズン2</option>
      <option value="elebest">[10万]エレぇベスト</option>
      <option disabled value="">シーズン4</option>
      <option value="legend">[10万]THE LEGEND</option>
      <option value="nanairo">[20万]七彩メモリーズ</option>
      <option value="diva">[30万]歌姫楽宴</option>
      <option disabled value="">W.I.N.G.</option>
      <option value="semifinal">W.I.N.G. 準決勝</option>
      <option value="final">W.I.N.G. 決勝</option>
    </select>

    <!--
    <select name="season" v-model="season" @change="changeSeason">
      <option value="season2">シーズン2</option>
      <option value="season4">シーズン4</option>
      <option value="wing">W.I.N.G.</option>
    </select>

    <template v-if="season == 'season2'">
      <select v-model="audition">
        <option value="elebest">エレぇベスト</option>
      </select>
    </template>
    <template v-else-if="season == 'season4'">
      <select v-model="audition">
        <option value="legend">THE LEGEND</option>
        <option value="nanairo">七彩メモリーズ</option>
        <option value="diva">歌姫楽宴</option>
      </select>
    </template>
    <template v-else>
      <select v-model="audition">
        <option value="semifinal">準決勝</option>
        <option value="final">決勝</option>
      </select>
    </template>
    -->

    <!-- ハリボテ

    <input v-model="turn" id="turn">
    <label for="turn">ターン</label> {{turn}}

    <div>
      落ちている審査員: {{ droppedJudge }}
      <input type="checkbox" id="vocal" value="vocal" v-model="droppedJudge">
      <label for="vocal">ボーカル</label>
      <input type="checkbox" id="dance" value="dance" v-model="droppedJudge">
      <label for="dance">ダンス</label>
      <input type="checkbox" id="visual" value="visual" v-model="droppedJudge">
      <label for="visual">ビジュアル</label>
    </div>

    -->

    <!-- ここより下は、コンポーネントにオブジェクトを渡せるようになったら
         コンポーネントを分ける -->

    <h2>{{ livedata[audition].name }}</h2>
    <p>基礎攻撃力: {{ livedata[audition].baseattack }}<br>
    審査員体力: {{ livedata[audition].health }}</p>
    <table>
      <thead>
        <tr><th>アピール</th><th>一致時攻撃力</th><th>不一致時攻撃力</th></tr>
      </thead>
      <tbody>
        <tr><td>Perfect</td><td>{{ Math.round(livedata[audition].baseattack * 1.5 * 2.0) }}</td><td>{{ Math.round(livedata[audition].baseattack * 1.5) }}</td></tr>
        <tr><td>Good</td><td>{{ Math.round(livedata[audition].baseattack * 1.1 * 2.0) }}</td><td>{{ Math.round(livedata[audition].baseattack * 1.1) }}</td></tr>
        <tr><td>Normal</td><td>{{ Math.round(livedata[audition].baseattack * 1.0 * 2.0) }}</td><td>{{ Math.round(livedata[audition].baseattack * 1.0) }}</td></tr>
        <tr><td>Bad</td><td>{{ Math.round(livedata[audition].baseattack * 0.5 * 2.0) }}</td><td>{{ Math.round(livedata[audition].baseattack * 0.5) }}</td></tr>
        <tr><td>思い出</td><td colspan="2">全属性に {{ Math.round(livedata[audition].baseattack * 2.0) }}</td></tr>
      </tbody>
    </table>

    <table>
      <thead>
        <tr><th></th><th>得意</th><th>傾向</th><th>優先度</th></tr>
      </thead>
      <tbody>
        <tr v-for="rivaldata in livedata[audition].idols" :key="rivaldata.id">
          <td>アイドル{{ rivaldata.num }} <img :alt="rivaldata.name" :src="require('./assets/img/idol/' + rivaldata.name + '.png')" width="64px" height="64px"></td>
          <td v-if="rivaldata.appeal == 'vocal'" style="background-color: #ff99be;">ボーカル</td>
          <td v-else-if="rivaldata.appeal == 'visual'" style="background-color: #ffe89e">ビジュアル</td>
          <td v-else style="background-color: #9ebeff">ダンス</td>

          <td v-if="rivaldata.type == 'spear'">スピア</td>
          <td v-else>変遷</td>

          <td>{{ rivaldata.priority }}</td>
        </tr>

      </tbody>
    </table>
    
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'app',
  components: {
  },
  data () {
    return {
      season: 'wing',
      audition: 'final',
      turn: '1',
      livedata: null,
      droppedJudge: [],
      currentAudition: []
    }
  },
  mounted () {
    axios
      .get('https://raw.githubusercontent.com/project-brightblue/wingcalc/master/src/assets/data/livedata.json')
      .then(response => (this.livedata = response.data))
  }
}
</script>

<style>
#app {
  margin-left: auto;
  margin-right: auto;
  max-width: 540px;
}
</style>
