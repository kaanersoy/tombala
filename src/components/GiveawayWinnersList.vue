<template>
  <div class="giveaway-information-wrapper" id="giveaway-winners">
    <div class="giveaway-information-header">
      <h5>Winner(s)</h5>
    </div>
    <div class="giveaway-information-content">
      <div class="giveaway-winner-row" v-for="winner in winners" :key="winner.id">
        <div class="box">
          <img :src="winner.snippet.topLevelComment.snippet.authorProfileImageUrl" width="80" height="80" alt="winnername">
        </div>
        <div class="box">
          <h5>
            <a :href="winner.snippet.topLevelComment.snippet.authorChannelUrl" target="_blank">
             {{winner.snippet.topLevelComment.snippet.authorDisplayName}}
            </a>
          </h5>
          <p>{{winner.snippet.topLevelComment.snippet.textDisplay}}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GiveawayWinnersList',
  props: {
    winners: {
      type: Array,
      required: true
    }
  }
}
</script>

<style scoped>
.giveaway-information-wrapper{}
.giveaway-information-wrapper .giveaway-information-header{}
.giveaway-information-wrapper .giveaway-information-header h5{
  font-size: 30px;
  text-align: center;
  color: rgb(var(--main-color));
  padding: 12px 0;
  margin: 12px 0;
  border-bottom: 2px solid rgba(var(--main-color), 0.3);
}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row{
  display: flex;
  align-items: center;
  margin: 6px 0;
  padding: 6px 0;
  border-bottom: 2px solid rgba(var(--main-color), 0.4);
}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row:last-child{
  border-bottom: none;
}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row .box{}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row .box:last-child{margin-left: 20px;}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row .box img{
  border-radius: 50%;
}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row .box h5{
  color: rgb(var(--main-color));
  font-size: 18px;
}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row .box h5 a{
  color: inherit;
}
.giveaway-information-wrapper .giveaway-information-content .giveaway-winner-row .box p{
  color: rgb(var(--contrasted-text));
  font-size: 15px;
}
</style>