<template lang="html">
  <div :val="value_">
    <div>
      <el-radio v-model="type" label="1" size="mini" border>{{ $t('cron.every_day') }}</el-radio>
    </div>
    <div>
      <el-radio v-model="type" label="5" size="mini" border>{{ $t('cron.not_set') }}</el-radio>
    </div>
    <div>
      <el-radio v-model="type" label="2" size="mini" border>{{ $t('cron.cycle') }}</el-radio>
      <span style="margin-left: 10px; margin-right: 5px;">{{ $t('cron.from') }}</span>
      <el-input-number v-model="cycle.start" :min="1" :max="31" size="mini" style="width: 100px;" @change="type = '2'" />
      <span style="margin-left: 5px; margin-right: 5px;">{{ $t('cron.to') }}</span>
      <el-input-number v-model="cycle.end" :min="2" :max="31" size="mini" style="width: 100px;" @change="type = '2'" />
      {{ $t('cron.day') }}
    </div>
    <div>
      <el-radio v-model="type" label="3" size="mini" border>{{ $t('cron.repeat') }}</el-radio>
      <span style="margin-left: 10px; margin-right: 5px;">{{ $t('cron.from') }}</span>
      <el-input-number v-model="loop.start" :min="1" :max="31" size="mini" style="width: 100px;" @change="type = '3'" />
      <span style="margin-left: 5px; margin-right: 5px;">{{ $t('cron.day_begin') }}</span>
      <el-input-number v-model="loop.end" :min="1" :max="31" size="mini" style="width: 100px;" @change="type = '3'" />
      {{ $t('cron.day_exec') }}
    </div>
    <div>
      <el-radio v-model="type" label="8" size="mini" border>{{ $t('cron.work_day') }}</el-radio>
      <span style="margin-left: 10px; margin-right: 5px;">{{ $t('cron.this_month') }}</span>
      <el-input-number v-model="work" :min="1" :max="31" size="mini" style="width: 100px;" @change="type = '8'" />
      {{ $t('cron.day_near_work_day') }}
    </div>
    <div>
      <el-radio v-model="type" label="6" size="mini" border>{{ $t('cron.this_week_last_day') }}</el-radio>
    </div>
    <div>
      <el-radio v-model="type" label="4" size="mini" border>{{ $t('cron.set') }}</el-radio>
      <el-checkbox-group v-model="appoint">
        <div v-for="i in 4" :key="i" style="margin-left: 10px;  line-height: 25px;">
          <el-checkbox v-for="j in 10" v-if="parseInt((i - 1) + '' + (j - 1)) < 32 && !(i === 1 && j === 1)" :key="j" :label="(i - 1) + '' + (j - 1)" @change="type = '4'" />
        </div>
      </el-checkbox-group>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: '?'
    }
  },
  data() {
    return {
      type: '5', // ??????
      cycle: { // ??????
        start: 0,
        end: 0
      },
      loop: { // ??????
        start: 0,
        end: 0
      },
      week: { // ?????????
        start: 0,
        end: 0
      },
      work: 0,
      last: 0,
      appoint: [] // ??????
    }
  },
  computed: {
    value_() {
      const result = []
      switch (this.type) {
        case '1': // ??????
          result.push('*')
          break
        case '2': // ??????
          result.push(`${this.cycle.start}-${this.cycle.end}`)
          break
        case '3': // ??????
          result.push(`${this.loop.start}/${this.loop.end}`)
          break
        case '4': // ??????
          result.push(this.appoint.join(','))
          break
        case '6': // ??????
          result.push(`${this.last === 0 ? '' : this.last}L`)
          break
        case '7': // ?????????
          result.push(`${this.week.start}#${this.week.end}`)
          break
        case '8': // ?????????
          result.push(`${this.work}W`)
          break
        default: // ?????????
          result.push('?')
          break
      }
      this.$emit('input', result.join(''))
      return result.join('')
    }
  },
  watch: {
    'value'(a, b) {
      this.updateVal()
    }
  },
  created() {
    this.updateVal()
  },
  methods: {
    updateVal() {
      if (!this.value) {
        return
      }
      if (this.value === '?') {
        this.type = '5'
      } else if (this.value.indexOf('-') !== -1) { // 2??????
        if (this.value.split('-').length === 2) {
          this.type = '2'
          this.cycle.start = this.value.split('-')[0]
          this.cycle.end = this.value.split('-')[1]
        }
      } else if (this.value.indexOf('/') !== -1) { // 3??????
        if (this.value.split('/').length === 2) {
          this.type = '3'
          this.loop.start = this.value.split('/')[0]
          this.loop.end = this.value.split('/')[1]
        }
      } else if (this.value.indexOf('*') !== -1) { // 1???
        this.type = '1'
      } else if (this.value.indexOf('L') !== -1) { // 6??????
        this.type = '6'
        this.last = this.value.replace('L', '')
      } else if (this.value.indexOf('#') !== -1) { // 7?????????
        if (this.value.split('#').length === 2) {
          this.type = '7'
          this.week.start = this.value.split('#')[0]
          this.week.end = this.value.split('#')[1]
        }
      } else if (this.value.indexOf('W') !== -1) { // 8?????????
        this.type = '8'
        this.work = this.value.replace('W', '')
      } else { // *
        this.type = '4'
        this.appoint = this.value.split(',')
      }
    }
  }
}
</script>

<style lang="css" scoped>
.el-checkbox+.el-checkbox {
    margin-left: 10px;
}
</style>
