<template>
  <div class="modal-card">
    <header class="modal-card-head">
      <p class="modal-card-title">
        {{ lang('Storage percent', percent) }}
      </p>
    </header>
    <section class="modal-card-body">
      
      <div class="table-container">
        <table class="table is-fullwidth">
          <tbody>
          <tr>
            <td colspan="2" style="text-align:center;border-bottom:none;">{{ lang('Total Space') }} : {{ total }}</td>
          </tr>
          <tr>
            <td colspan="2" style="border-bottom:none;">
              <progress class="progress is-info" value="{{ percent }}" max="100">{{ percent }}%</progress>
            </td>
          </tr>
          <tr>
            <td>{{ lang('Used Space') }} : {{ used }}</td>
            <td class="has-text-right" style="width:50%">{{ lang('Free Space') }} : {{ free }}</td>
          </tr>
          </tbody>
        </table>
      </div>
      
    </section>
    <footer class="modal-card-foot">
      <button class="button" type="button" @click="$parent.close()">
        {{ lang('Close') }}
      </button>
    </footer>
  </div>
</template>

<script>
import api from '../../api/api'
//import _ from 'lodash'

export default {
  name: 'Storage',
  data() {
    return {
      total: 0,
      used: 0,
      percent: 0,
      free: 0,
    }
  },
  mounted() {
    api.getStorageInfo()
      .then((res) => {
        this.total = this.humanFileSize(res.total)
        this.used = this.humanFileSize(res.used)
        this.free = this.humanFileSize(res.total - res.used)
        this.percent = Math.round(res.used * 10000 / res.total) / 100
      })
      .catch(error => this.handleError(error))
  },
  methods: {
    // Source: https://stackoverflow.com/a/14919494/1720332
    /* Format bytes as human-readable text.
     * 
     * @param bytes Number of bytes.
     * @param si True to use metric (SI) units, aka powers of 1000. False to use 
     *           binary (IEC), aka powers of 1024.
     * @param dp Number of decimal places to display.
     * 
     * @return Formatted string.
     */
    humanFileSize(bytes, si=false, dp=1) {
      const thresh = si ? 1000 : 1024
      if (Math.abs(bytes) < thresh) {
        return bytes / 1000 + ' KB'
      }
      const units = si 
        ? ['kB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB'] 
        : ['kB', 'MB', 'GB', 'TB', 'PB', 'EB', 'ZB', 'YB']
      let u = -1
      const r = 10**dp
      do {
        bytes /= thresh
        ++u
      } while (Math.round(Math.abs(bytes) * r) / r >= thresh && u < units.length - 1)
      return bytes.toFixed(dp) + ' ' + units[u]
    }
  },
}
</script>
