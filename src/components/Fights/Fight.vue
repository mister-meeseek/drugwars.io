<template>
  <div class="border-bottom pb-4 mb-4 columns">
    <div class="columns text-center">
      <div class="column col-5">
        <Avatar :size="80" :username="fight.username"/>
        <div class="username mb-4">{{ fight.username }}</div>
        <div class="mb-4" v-if="json.attacker">
          <Army
            v-if="json.attacker.units"
            :units="json.attacker.units"
            :withDead="true"
          />
        </div>
        <p class="mb-4">{{ fight.message }}</p>
      </div>
      <div class="column col-2">
        <div class="mt-4" v-if="result">
          <div class="button button-green result" v-if="result === 'win'">
            Win
          </div>
          <div class="button result" v-if="result === 'draw'">
            Draw
          </div>
          <div class="button button-red result" v-if="result === 'lost'">
            Lost
          </div>
        </div>
        <h1 class="mt-3" v-else>VS</h1>
        <span class="mt-3" v-if="timeToWait">
          Start in {{ timeToWait | ms }}
        </span>
      </div>
      <div class="column col-5">
        <Avatar :size="80" :username="fight.target"/>
        <div class="username mb-4">{{ fight.target }}</div>
        <div class="mb-4" v-if="json.target">
          <Army
            v-if="json.target.units"
            :units="json.target.units"
            :withDead="true"
          />
          <FightsLoot
            v-if="json.target.loot"
            :stolenResources="json.target.loot"
          />
        </div>
      </div>
    </div>
    <div>
      <span class="mr-2">
        Fight
        #{{ fight.fight_key.slice(0, 10) }}
        {{ fight.is_done ? 'ended' : 'incoming' }}
      </span>
      <span v-if="!fight.is_stable" class="mr-2">
        (pending confirmation)
      </span>
      <div>
             Start :  {{start}} - End : {{end}}
             </div>
    </div>
  </div>
</template>

<script>
import { jsonParse } from '@/helpers/utils';

export default {
  props: ['fight'],
  computed: {
    timeToWait() {
      const timeToWait = this.fight.timestamp_end * 1000 - this.$store.state.ui.timestamp;
      return timeToWait > 0 ? timeToWait : 0;
    },
    start() {
      const start = new Date(this.fight.timestamp_start * 1000).toLocaleString();
      return start;
    },
    end() {
      const end = new Date(this.fight.timestamp_end * 1000).toLocaleString();
      return end;
    },
    result() {
      let result;
      const isAuthor = this.fight.username === this.username;
      if (this.fight.result === 1) {
        result = isAuthor ? 'win' : 'lost';
      } else if (this.fight.result === 3) {
        result = !isAuthor ? 'win' : 'lost';
      } else if (this.fight.result === 2) {
        result = 'draw';
      }
      return result;
    },
    username() {
      return this.$store.state.auth.username;
    },
    json() {
      return jsonParse(this.fight.json);
    },
  },
};
</script>

<style scoped type="less">
@import '../../vars.less';

.result {
  font-size: 36px;
  padding: 10px;
  height: 50px;
  background-size: cover !important;
}
</style>
